---
title: Não é possível conectar com RDP a uma VM do Windows no Azure | Microsoft Docs
description: Solucionar problemas quando não for possível se conectar à sua máquina virtual do Windows no Azure usando Área de Trabalho Remota
keywords: Erro de área de trabalho remota, erro de conexão de área de trabalho remota, não é possível conectar-se à VM, solução de problemas
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 0d740f8e-98b8-4e55-bb02-520f604f5b18
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/23/2018
ms.author: akjosh
ms.openlocfilehash: 0a88c1e4d357f2919635e36a223e79b0407c0b8b
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168745"
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Solucionar problemas de conexões Área de Trabalho Remota a uma máquina virtual do Azure
A ligação RDP (Remote Desktop Protocol) à sua máquina virtual (VM) do Azure baseada em Windows pode falhar por várias razões, impedindo o seu acesso à VM. O problema poderá estar no serviço Ambiente de Trabalho Remoto na VM, na ligação de rede ou no cliente do Ambiente de Trabalho Remoto no computador anfitrião. Este artigo orienta você por alguns dos métodos mais comuns para resolver problemas de conexão de RDP. 

Se precisar de mais ajuda a qualquer momento neste artigo, você poderá entrar em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow do MSDN](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Etapas de solução de problemas rápidos
Após cada etapa de solução de problemas, tente reconectar-se à VM:

1. Redefinir configuração de Área de Trabalho Remota.
2. Verifique as regras do grupo de segurança de rede/pontos de extremidade dos serviços de nuvem.
3. Examine os logs do console da VM.
4. Redefina a NIC para a VM.
5. Verifique o Resource Health da VM.
6. Redefina a senha da VM.
7. Reinicie a VM.
8. Reimplante sua VM.

Continue lendo se precisar de etapas e explicações mais detalhadas. Verifique se o equipamento de rede local, como roteadores e firewalls, não está bloqueando a porta TCP de saída 3389, conforme observado em [cenários de solução de problemas de RDP detalhados](detailed-troubleshoot-rdp.md).

> [!TIP]
> Se o botão **conectar** para sua VM estiver esmaecido no portal e você não estiver conectado ao Azure por meio de uma [rota expressa](../../expressroute/expressroute-introduction.md) ou uma conexão [VPN site a site](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) , você precisará criar e atribuir à VM um endereço IP público antes de poder usar o RDP. Pode ler mais sobre [endereços IP públicos no Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Maneiras de solucionar problemas de RDP
Você pode solucionar problemas de VMs criadas usando o modelo de implantação do Resource Manager usando um dos seguintes métodos:

* Portal do Azure-ótimo se você precisar redefinir rapidamente as credenciais de usuário ou a configuração de RDP e não tiver as ferramentas do Azure instaladas.
* Azure PowerShell-se você estiver familiarizado com um prompt do PowerShell, redefina rapidamente a configuração do RDP ou as credenciais do usuário usando os cmdlets Azure PowerShell.

Você também pode encontrar etapas sobre como solucionar problemas de VMs criadas usando o [modelo de implantação clássico](#troubleshoot-vms-created-using-the-classic-deployment-model).

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Solucionar problemas usando o portal do Azure
Após cada etapa de solução de problemas, tente se conectar à VM novamente. Se você ainda não conseguir se conectar, tente a próxima etapa.

1. **Redefina a conexão RDP**. Essa etapa de solução de problemas redefine a configuração de RDP quando conexões remotas são desabilitadas ou as regras de firewall do Windows estão bloqueando o RDP, por exemplo.
   
    Selecione sua VM no portal do Azure. Role para baixo o painel configurações até a seção **suporte + solução de problemas** , próximo à parte inferior da lista. Clique no botão **Redefinir senha** . Defina o **modo** para **Redefinir somente configuração** e, em seguida, clique no botão **Atualizar** :
   
    ![Redefinir a configuração de RDP no portal do Azure](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **Verifique as regras do grupo de segurança de rede**. Utilize a [verificação do fluxo de IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) para confirmar se uma regra num Grupo de Segurança de Rede está a bloquear o tráfego de ou para uma máquina virtual. Você também pode examinar as regras de grupo de segurança efetivas para garantir que a regra "permitir" NSG de entrada exista e seja priorizada para a porta RDP (padrão 3389). Para obter mais informações, consulte [usando regras de segurança efetivas para solucionar problemas de fluxo de tráfego de VM](../../virtual-network/diagnose-network-traffic-filter-problem.md).

3. **Examine o diagnóstico de inicialização da VM**. Esta etapa de solução de problemas revisa os logs do console da VM para determinar se a VM está relatando um problema. Nem todas as VMs têm o diagnóstico de inicialização habilitado, portanto, essa etapa de solução de problemas pode ser opcional.
   
    Etapas de solução de problemas específicas estão além do escopo deste artigo, mas podem indicar um problema maior que está afetando a conectividade RDP. Para obter mais informações sobre como examinar os logs do console e a captura de tela da VM, consulte [diagnóstico de inicialização para VMs](boot-diagnostics.md).

4. **Redefina a NIC para a VM**. Para obter mais informações, consulte [como redefinir NIC para VM do Windows do Azure](../windows/reset-network-interface.md).
5. **Verifique o Resource Health da VM**. Essa etapa de solução de problemas verifica se não há problemas conhecidos com a plataforma do Azure que podem afetar a conectividade com a VM.
   
    Selecione sua VM no portal do Azure. Role para baixo o painel configurações até a seção **suporte + solução de problemas** , próximo à parte inferior da lista. Clique no botão **integridade do recurso** . Uma VM íntegra é relatada como **disponível**:
   
    ![Verificar a integridade do recurso da VM no portal do Azure](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **Redefinir as credenciais do usuário**. Essa etapa de solução de problemas redefine a senha em uma conta de administrador local quando você não tem certeza ou esqueceu as credenciais.  Depois de fazer logon na VM, você deve redefinir a senha para esse usuário.
   
    Selecione sua VM no portal do Azure. Role para baixo o painel configurações até a seção **suporte + solução de problemas** , próximo à parte inferior da lista. Clique no botão **Redefinir senha** . Verifique se o **modo** está definido como **Redefinir senha** e, em seguida, insira seu nome de usuário e uma nova senha. Por fim, clique no botão **Atualizar** :
   
    ![Redefinir as credenciais do usuário no portal do Azure](./media/troubleshoot-rdp-connection/reset-password.png)
7. **Reinicie a VM**. Essa etapa de solução de problemas pode corrigir quaisquer problemas subjacentes que a própria VM esteja tendo.
   
    Selecione sua VM no portal do Azure e clique na guia **visão geral** . Clique no botão **reiniciar** :
   
    ![Reinicie a VM no portal do Azure](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **Reimplante sua VM**. Esta etapa de solução de problemas reimplanta sua VM em outro host no Azure para corrigir qualquer problema de plataforma ou rede subjacente.
   
    Selecione sua VM no portal do Azure. Role para baixo o painel configurações até a seção **suporte + solução de problemas** , próximo à parte inferior da lista. Clique no botão **reimplantar** e, em seguida, clique em **reimplantar**:
   
    ![Reimplantar a VM no portal do Azure](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    Após a conclusão dessa operação, os dados do disco efêmero são perdidos e os endereços IP dinâmicos associados à VM são atualizados.

9. **Verifique o roteamento**. Use a capacidade do [próximo salto](../../network-watcher/network-watcher-check-next-hop-portal.md) do observador de rede para confirmar se uma rota não está impedindo que o tráfego seja roteado para ou de uma máquina virtual. Você também pode examinar rotas efetivas para ver todas as rotas efetivas para uma interface de rede. Para obter mais informações, consulte [usando rotas efetivas para solucionar problemas de fluxo de tráfego de VM](../../virtual-network/diagnose-network-routing-problem.md).

10. Verifique se qualquer firewall local ou firewall em seu computador permite o tráfego de saída TCP 3389 para o Azure.

Se você ainda estiver encontrando problemas de RDP, poderá [abrir uma solicitação de suporte](https://azure.microsoft.com/support/options/) ou ler [conceitos e etapas de solução de problemas de RDP mais detalhados](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-using-azure-powershell"></a>Solucionar problemas usando Azure PowerShell
Se você ainda não fez isso, [Instale e configure as Azure PowerShell mais recentes](/powershell/azure/overview).

Os exemplos a seguir usam variáveis como `myResourceGroup`, `myVM`e `myVMAccessExtension`. Substitua esses nomes de variáveis e locais por seus próprios valores.

> [!NOTE]
> Você redefine as credenciais do usuário e a configuração de RDP usando o cmdlet [set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) do PowerShell. Nos exemplos a seguir, `myVMAccessExtension` é um nome que você especifica como parte do processo. Se você já trabalhou com o VMAccessAgent, você pode obter o nome da extensão existente usando `Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"` para verificar as propriedades da VM. Para exibir o nome, examine a seção ' extensões ' da saída.

Após cada etapa de solução de problemas, tente se conectar à VM novamente. Se você ainda não conseguir se conectar, tente a próxima etapa.

1. **Redefina a conexão RDP**. Essa etapa de solução de problemas redefine a configuração de RDP quando conexões remotas são desabilitadas ou as regras de firewall do Windows estão bloqueando o RDP, por exemplo.
   
    O exemplo a seguir redefine a conexão RDP em uma VM `myVM` chamada `WestUS` no local e no grupo de recursos denominado `myResourceGroup`:
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **Verifique as regras do grupo de segurança de rede**. Esta etapa de solução de problemas verifica se você tem uma regra em seu grupo de segurança de rede para permitir o tráfego RDP. A porta padrão para RDP é a porta TCP 3389. Uma regra para permitir o tráfego RDP pode não ser criada automaticamente quando você cria sua VM.
   
    Primeiro, atribua todos os dados de configuração do seu grupo de segurança de `$rules` rede à variável. O exemplo a seguir obtém informações sobre o grupo de segurança de `myNetworkSecurityGroup` rede chamado no grupo de `myResourceGroup`recursos denominado:
   
    ```powershell
    $rules = Get-AzNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    Agora, exiba as regras que estão configuradas para esse grupo de segurança de rede. Verifique se existe uma regra para permitir a porta TCP 3389 para conexões de entrada da seguinte maneira:
   
    ```powershell
    $rules.SecurityRules
    ```
   
    O exemplo a seguir mostra uma regra de segurança válida que permite o tráfego RDP. Você pode ver `Protocol`, `DestinationPortRange`, `Access`e `Direction` estão configurados corretamente:
   
    ```powershell
    Name                     : default-allow-rdp
    Id                       : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/default-allow-rdp
    Etag                     : 
    ProvisioningState        : Succeeded
    Description              : 
    Protocol                 : TCP
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : *
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 1000
    Direction                : Inbound
    ```
   
    Se você não tiver uma regra que permita o tráfego RDP, [crie uma regra de grupo de segurança de rede](../windows/nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Permitir porta TCP 3389.
3. **Redefinir as credenciais do usuário**. Essa etapa de solução de problemas redefine a senha na conta de administrador local que você especifica quando não tem certeza ou se esqueceu das credenciais.
   
    Primeiro, especifique o nome de usuário e uma nova senha atribuindo credenciais à variável `$cred` da seguinte maneira:
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Agora, atualize as credenciais em sua VM. O exemplo a seguir atualiza as credenciais em uma VM `myVM` chamada `WestUS` no local e no grupo de recursos denominado `myResourceGroup`:
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Reinicie a VM**. Essa etapa de solução de problemas pode corrigir quaisquer problemas subjacentes que a própria VM esteja tendo.
   
    O exemplo a seguir reinicia a VM chamada `myVM` no grupo de recursos chamado `myResourceGroup`:
   
    ```powershell
    Restart-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **Reimplante sua VM**. Esta etapa de solução de problemas reimplanta sua VM em outro host no Azure para corrigir qualquer problema de plataforma ou rede subjacente.
   
    O exemplo a seguir reimplanta a VM chamada `myVM` `WestUS` no local e no grupo de recursos denominado `myResourceGroup`:
   
    ```powershell
    Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

6. **Verifique o roteamento**. Use a capacidade do [próximo salto](../../network-watcher/network-watcher-check-next-hop-portal.md) do observador de rede para confirmar se uma rota não está impedindo que o tráfego seja roteado para ou de uma máquina virtual. Você também pode examinar rotas efetivas para ver todas as rotas efetivas para uma interface de rede. Para obter mais informações, consulte [usando rotas efetivas para solucionar problemas de fluxo de tráfego de VM](../../virtual-network/diagnose-network-routing-problem.md).

7. Verifique se qualquer firewall local ou firewall em seu computador permite o tráfego de saída TCP 3389 para o Azure.

Se você ainda estiver encontrando problemas de RDP, poderá [abrir uma solicitação de suporte](https://azure.microsoft.com/support/options/) ou ler [conceitos e etapas de solução de problemas de RDP mais detalhados](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Solucionar problemas de VMs criadas usando o modelo de implantação clássico
Após cada etapa de solução de problemas, tente reconectar-se à VM.

1. **Redefina a conexão RDP**. Essa etapa de solução de problemas redefine a configuração de RDP quando conexões remotas são desabilitadas ou as regras de firewall do Windows estão bloqueando o RDP, por exemplo.
   
    Selecione sua VM no portal do Azure. Clique em **... Botão mais** e clique em **Redefinir acesso remoto**:
   
    ![Redefinir a configuração de RDP no portal do Azure](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Verifique os pontos de extremidade dos serviços de nuvem**. Esta etapa de solução de problemas verifica se você tem pontos de extremidade em seus serviços de nuvem para permitir o tráfego RDP. A porta padrão para RDP é a porta TCP 3389. Uma regra para permitir o tráfego RDP pode não ser criada automaticamente quando você cria sua VM.
   
   Selecione sua VM no portal do Azure. Clique no botão **pontos de extremidade** para exibir os pontos de extremidade atualmente configurados para sua VM. Verifique se existem pontos de extremidade que permitem o tráfego RDP na porta TCP 3389.
   
   O exemplo a seguir mostra pontos de extremidade válidos que permitem o tráfego RDP:
   
   ![Verificar os pontos de extremidade dos serviços de nuvem no portal do Azure](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Se você não tiver um ponto de extremidade que permita o tráfego RDP, [crie um ponto de extremidade dos serviços de nuvem](../windows/classic/setup-endpoints.md). Permitir TCP para a porta privada 3389.
3. **Examine o diagnóstico de inicialização da VM**. Esta etapa de solução de problemas revisa os logs do console da VM para determinar se a VM está relatando um problema. Nem todas as VMs têm o diagnóstico de inicialização habilitado, portanto, essa etapa de solução de problemas pode ser opcional.
   
    Etapas de solução de problemas específicas estão além do escopo deste artigo, mas podem indicar um problema maior que está afetando a conectividade RDP. Para obter mais informações sobre como examinar os logs do console e a captura de tela da VM, consulte [diagnóstico de inicialização para VMs](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).
4. **Verifique o Resource Health da VM**. Essa etapa de solução de problemas verifica se não há problemas conhecidos com a plataforma do Azure que podem afetar a conectividade com a VM.
   
    Selecione sua VM no portal do Azure. Role para baixo o painel configurações até a seção **suporte + solução de problemas** , próximo à parte inferior da lista. Clique no botão **Resource Health** . Uma VM íntegra é relatada como **disponível**:
   
    ![Verificar a integridade do recurso da VM no portal do Azure](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Redefinir as credenciais do usuário**. Essa etapa de solução de problemas redefine a senha na conta de administrador local que você especifica quando não tem certeza ou esqueceu as credenciais.  Depois de fazer logon na VM, você deve redefinir a senha para esse usuário.
   
    Selecione sua VM no portal do Azure. Role para baixo o painel configurações até a seção **suporte + solução de problemas** , próximo à parte inferior da lista. Clique no botão **Redefinir senha** . Insira seu nome de usuário e uma nova senha. Por fim, clique no botão **salvar** :
   
    ![Redefinir as credenciais do usuário no portal do Azure](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **Reinicie a VM**. Essa etapa de solução de problemas pode corrigir quaisquer problemas subjacentes que a própria VM esteja tendo.
   
    Selecione sua VM no portal do Azure e clique na guia **visão geral** . Clique no botão **reiniciar** :
   
    ![Reinicie a VM no portal do Azure](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

7. Verifique se qualquer firewall local ou firewall em seu computador permite o tráfego de saída TCP 3389 para o Azure.

Se você ainda estiver encontrando problemas de RDP, poderá [abrir uma solicitação de suporte](https://azure.microsoft.com/support/options/) ou ler [conceitos e etapas de solução de problemas de RDP mais detalhados](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-specific-rdp-errors"></a>Solucionar erros de RDP específicos
Você pode encontrar uma mensagem de erro específica ao tentar se conectar à VM via RDP. A seguir estão as mensagens de erro mais comuns:

* [A sessão remota foi desconectada porque não há servidores de licença área de trabalho remota disponíveis para fornecer uma licença](troubleshoot-specific-rdp-errors.md#rdplicense).
* [Área de trabalho remota não pode localizar o computador "nome"](troubleshoot-specific-rdp-errors.md#rdpname).
* [Ocorreu um erro de autenticação. Não é possível contatar](troubleshoot-specific-rdp-errors.md#rdpauth)a autoridade de segurança local.
* [Erro de segurança do Windows: Suas credenciais não funcionaram](troubleshoot-specific-rdp-errors.md#wincred).
* [Este computador não pode se conectar ao computador remoto](troubleshoot-specific-rdp-errors.md#rdpconnect).

## <a name="additional-resources"></a>Recursos adicionais
Se nenhum desses erros ocorreu e você ainda não conseguir se conectar à VM por meio de Área de Trabalho Remota, leia o [Guia de solução de problemas detalhado para área de trabalho remota](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para obter as etapas de solução de problemas de acesso a aplicativos executados em uma VM, consulte [solucionar problemas de acesso a um aplicativo em execução em uma VM do Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Se você estiver tendo problemas ao usar o Secure Shell (SSH) para se conectar a uma VM do Linux no Azure, consulte [solucionar problemas de conexões SSH para uma VM do Linux no Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


