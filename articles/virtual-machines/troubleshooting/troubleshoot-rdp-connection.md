---
title: Não é possível ligar-se ao RDP a um Windows VM em Azure [ Microsoft Docs
description: Problemas de resolução de problemas quando não é possível ligar-se à sua máquina virtual Windows em Azure usando o Remote Desktop
keywords: Erro remoto no ambiente de trabalho,erro de ligação remota de ambiente de trabalho,não pode ligar-se a VM,resolução remota de problemas no ambiente de trabalho
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
ms.openlocfilehash: cbca8e631da8b99aa0ea4bdc6d099f3dbd2ed9b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77916613"
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Resolver problemas de ligações de Ambiente de Trabalho Remoto a máquinas virtuais do Azure
A ligação RDP (Remote Desktop Protocol) à sua máquina virtual (VM) do Azure baseada em Windows pode falhar por várias razões, impedindo o seu acesso à VM. O problema poderá estar no serviço Ambiente de Trabalho Remoto na VM, na ligação de rede ou no cliente do Ambiente de Trabalho Remoto no computador anfitrião. Este artigo serve de orientação ao longo de alguns dos métodos mais comuns para resolver problemas de ligação RDP. 

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.

 

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Passos rápidos de resolução de problemas
Após cada passo de resolução de problemas, tente voltar a ligar-se ao VM:

1. Redefinir a configuração do ambiente de trabalho remoto.
2. Verifique as regras do Grupo de Segurança da Rede / Pontos finais dos Serviços cloud.
3. Reveja os registos de consolas VM.
4. Redefinir o NIC para o VM.
5. Verifique a Saúde dos Recursos VM.
6. Redefinir a sua senha VM.
7. Reinicie o seu VM.
8. Recoloque o seu VM.

Continuar a ler Se precisar de passos e explicações mais detalhadas. Verifique se equipamentos de rede locais, como routers e firewalls, não estão a bloquear a porta 3389 de saída do TCP, como se nota em [cenários detalhados](detailed-troubleshoot-rdp.md)de resolução de problemas do RDP.

> [!TIP]
> Se o botão **Connect** para o seu VM estiver acinzentado no portal e não estiver ligado ao Azure através de uma [rota expressa](../../expressroute/expressroute-introduction.md) ou ligação [VPN site-to-site,](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) precisa de criar e atribuir ao seu VM um endereço IP público antes de poder utilizar rdp. Pode ler mais sobre [endereços IP públicos no Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Formas de resolver problemas com questões do PDR
Pode resolver problemas com vMs criados utilizando o modelo de implementação do Gestor de Recursos utilizando um dos seguintes métodos:

* Portal Azure - ótimo se precisar de redefinir rapidamente a configuração de RDP ou credenciais de utilizador e não tiver as ferramentas Azure instaladas.
* Azure PowerShell - se estiver confortável com um pedido PowerShell, repor rapidamente a configuração RDP ou as credenciais de utilizador utilizando os cmdlets Azure PowerShell.

Também pode encontrar passos na resolução de problemas de VMs criados usando o modelo de [implementação Classic](#troubleshoot-vms-created-using-the-classic-deployment-model).

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Resolução de problemas usando o portal Azure
Depois de cada passo de resolução de problemas, tente ligar-se novamente ao seu VM. Se ainda não consegue ligar, tente o próximo passo.

1. **Redefinir a sua ligação RDP**. Este passo de resolução de problemas repõe a configuração RDP quando as Ligações Remotas são desativadas ou as regras do Windows Firewall estão a bloquear rdp, por exemplo.
   
    Selecione o seu VM no portal Azure. Desloque-se para baixo do painel de definições para a secção de resolução de **problemas +** perto da parte inferior da lista. Clique no botão de **palavra-passe Reset.** Ajuste a configuração do **Modo** para **redefinir apenas** e, em seguida, clique no botão **Atualizar:**
   
    ![Redefinir a configuração do PDR no portal Azure](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **Verifique as regras do Grupo**de Segurança da Rede . Utilize a [verificação do fluxo de IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) para confirmar se uma regra num Grupo de Segurança de Rede está a bloquear o tráfego de ou para uma máquina virtual. Também pode rever regras eficazes do grupo de segurança para garantir a existência da regra NSG de entrada e é priorizada para a porta RDP (padrão 3389). Para mais informações, consulte a utilização de regras de [segurança eficazes para resolver o fluxo](../../virtual-network/diagnose-network-traffic-filter-problem.md)de tráfego vm .

3. **Reveja**os diagnósticos de arranque vm . Este passo de resolução de problemas analisa os registos da consola VM para determinar se o VM está a reportar um problema. Nem todos os VMs têm diagnósticos de arranque ativados, por isso este passo de resolução de problemas pode ser opcional.
   
    Os passos específicos de resolução de problemas estão fora do âmbito deste artigo, mas podem indicar um problema mais vasto que está a afetar a conectividade rdp. Para obter mais informações sobre a revisão dos registos das consolas e da imagem vM, consulte [Boot Diagnostics para VMs](boot-diagnostics.md).

4. **Redefinir o NIC para o VM**. Para mais informações, consulte [como redefinir nic para Azure Windows VM](../windows/reset-network-interface.md).
5. **Verifique a Saúde dos Recursos VM**. Este passo de resolução de problemas verifica que não existem problemas conhecidos com a plataforma Azure que possam ter impacto na conectividade com o VM.
   
    Selecione o seu VM no portal Azure. Desloque-se para baixo do painel de definições para a secção de resolução de **problemas +** perto da parte inferior da lista. Clique no botão **de saúde** do Recurso. Um VM saudável relata como estando **disponível:**
   
    ![Verifique a saúde dos recursos VM no portal Azure](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **Redefinir as credenciais de utilizador**. Este passo de resolução de problemas repõe a palavra-passe numa conta de administrador local quando não tem a certeza ou se esqueceu das credenciais.  Depois de ter iniciado o início do VM, deve redefinir a palavra-passe para esse utilizador.
   
    Selecione o seu VM no portal Azure. Desloque-se para baixo do painel de definições para a secção de resolução de **problemas +** perto da parte inferior da lista. Clique no botão de **palavra-passe Reset.** Certifique-se de que o **Modo** está definido para redefinir a **palavra-passe** e, em seguida, introduzir o seu nome de utilizador e uma nova senha. Por fim, clique no botão **Atualizar:**
   
    ![Redefinir as credenciais de utilizador no portal Azure](./media/troubleshoot-rdp-connection/reset-password.png)
7. **Reinicie o seu VM**. Este passo de resolução de problemas pode corrigir quaisquer problemas subjacentes que o próprio VM está a ter.
   
    Selecione o seu VM no portal Azure e clique no separador **'Visão Geral.Clique** no botão **Reiniciar':**
   
    ![Reinicie o VM no portal Azure](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **Reutilizar o seu VM**. Este passo de resolução de problemas reimplanta o seu VM para outro anfitrião dentro do Azure para corrigir quaisquer problemas subjacentes à plataforma ou à rede.
   
    Selecione o seu VM no portal Azure. Desloque-se para baixo do painel de definições para a secção de resolução de **problemas +** perto da parte inferior da lista. Clique no botão **Reenviar** e, em seguida, clique em **Recolocar:**
   
    ![Reutilizar o VM no portal Azure](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    Após esta operação terminar, os dados do disco efémero são perdidos e os endereços IP dinâmicos que estão associados ao VM são atualizados.

9. **Verifique o encaminhamento**. Utilize a capacidade de [salto Seguinte](../../network-watcher/network-watcher-check-next-hop-portal.md) do Network Watcher para confirmar que uma rota não está a impedir que o tráfego seja encaminhado para ou para uma máquina virtual. Também pode rever rotas eficazes para ver todas as rotas eficazes para uma interface de rede. Para mais informações, consulte [Utilizar rotas eficazes para resolver o fluxo](../../virtual-network/diagnose-network-routing-problem.md)de tráfego vm .

10. Certifique-se de que qualquer firewall no local, ou firewall no seu computador, permite tráfego de saída TCP 3389 para Azure.

Se ainda estiver a encontrar problemas de PDR, pode [abrir um pedido](https://azure.microsoft.com/support/options/) de apoio ou ler [conceitos e passos mais detalhados do RDP.](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="troubleshoot-using-azure-powershell"></a>Resolver problemas com o Azure PowerShell
Se ainda não o fez, [instale e configure o mais recente Azure PowerShell](/powershell/azure/overview).

Os exemplos seguintes utilizam `myResourceGroup` `myVM`variáveis `myVMAccessExtension`como, e . Substitua estes nomes e locais variáveis pelos seus próprios valores.

> [!NOTE]
> Redefinir as credenciais de utilizador e a configuração RDP utilizando o cmdlet [Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) PowerShell. Nos exemplos seguintes, `myVMAccessExtension` é um nome que especifica como parte do processo. Se já trabalhou com o VMAccessAgent, pode obter o nome `Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"` da extensão existente utilizando para verificar as propriedades do VM. Para ver o nome, consulte na secção 'Extensões' da saída.

Depois de cada passo de resolução de problemas, tente ligar-se novamente ao seu VM. Se ainda não consegue ligar, tente o próximo passo.

1. **Redefinir a sua ligação RDP**. Este passo de resolução de problemas repõe a configuração RDP quando as Ligações Remotas são desativadas ou as regras do Windows Firewall estão a bloquear rdp, por exemplo.
   
    O exemplo seguinte repõe a ligação RDP num VM nomeado `myVM` no `WestUS` local e no grupo de recursos denominado: `myResourceGroup`
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **Verifique as regras do Grupo**de Segurança da Rede . Este passo de resolução de problemas verifica que tem uma regra no seu Grupo de Segurança de Rede para permitir o tráfego de RDP. A porta padrão para RDP é a porta TCP 3389. Uma regra para permitir o tráfego de RDP pode não ser criada automaticamente quando criar o seu VM.
   
    Em primeiro lugar, atribua todos os `$rules` dados de configuração do seu Grupo de Segurança de Rede à variável. O exemplo seguinte obtém informações `myNetworkSecurityGroup` sobre o Grupo `myResourceGroup`de Segurança da Rede nomeado no grupo de recursos denominado:
   
    ```powershell
    $rules = Get-AzNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    Agora, veja as regras que estão configuradas para este Grupo de Segurança da Rede. Verificar se existe uma regra que permita a porta TCP 3389 para ligações de entrada da seguinte forma:
   
    ```powershell
    $rules.SecurityRules
    ```
   
    O exemplo que se segue mostra uma regra de segurança válida que permite o tráfego de RDP. Pode `Protocol`ver, `DestinationPortRange` `Access`e `Direction` estão configurados corretamente:
   
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
   
    Se não tiver uma regra que permita o tráfego de RDP, [crie uma regra do Grupo](../windows/nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)de Segurança da Rede . Permita a porta TCP 3389.
3. **Redefinir as credenciais de utilizador**. Este passo de resolução de problemas repõe a palavra-passe na conta do administrador local que especifica quando não tem a certeza ou esqueceu as credenciais.
   
    Em primeiro lugar, especifique o nome `$cred` de utilizador e uma nova palavra-passe atribuindo credenciais à variável da seguinte forma:
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Agora, atualize as credenciais no seu VM. O exemplo seguinte atualiza as credenciais `myVM` de `WestUS` um VM nomeado `myResourceGroup`no local e no grupo de recursos denominado:
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Reinicie o seu VM**. Este passo de resolução de problemas pode corrigir quaisquer problemas subjacentes que o próprio VM está a ter.
   
    O exemplo seguinte reinicia `myVM` o VM `myResourceGroup`nomeado no grupo de recursos denominado:
   
    ```powershell
    Restart-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **Reutilizar o seu VM**. Este passo de resolução de problemas reimplanta o seu VM para outro anfitrião dentro do Azure para corrigir quaisquer problemas subjacentes à plataforma ou à rede.
   
    O exemplo seguinte reimplanta `myVM` o `WestUS` VM nomeado no `myResourceGroup`local e no grupo de recursos denominado:
   
    ```powershell
    Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

6. **Verifique o encaminhamento**. Utilize a capacidade de [salto Seguinte](../../network-watcher/network-watcher-check-next-hop-portal.md) do Network Watcher para confirmar que uma rota não está a impedir que o tráfego seja encaminhado para ou para uma máquina virtual. Também pode rever rotas eficazes para ver todas as rotas eficazes para uma interface de rede. Para mais informações, consulte [Utilizar rotas eficazes para resolver o fluxo](../../virtual-network/diagnose-network-routing-problem.md)de tráfego vm .

7. Certifique-se de que qualquer firewall no local, ou firewall no seu computador, permite tráfego de saída TCP 3389 para Azure.

Se ainda estiver a encontrar problemas de PDR, pode [abrir um pedido](https://azure.microsoft.com/support/options/) de apoio ou ler [conceitos e passos mais detalhados do RDP.](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>VMs de resolução de problemas criados usando o modelo de implementação Clássico

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


Depois de cada passo de resolução de problemas, tente voltar a ligar-se ao VM.

1. **Redefinir a sua ligação RDP**. Este passo de resolução de problemas repõe a configuração RDP quando as Ligações Remotas são desativadas ou as regras do Windows Firewall estão a bloquear rdp, por exemplo.
   
    Selecione o seu VM no portal Azure. Clique no **... Mais** botão, em seguida, clique em **Redefinir Acesso Remoto:**
   
    ![Redefinir a configuração do PDR no portal Azure](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Verifique os pontos finais dos Serviços cloud**. Este passo de resolução de problemas verifica que você tem pontos finais nos seus Serviços cloud para permitir o tráfego de RDP. A porta padrão para RDP é a porta TCP 3389. Uma regra para permitir o tráfego de RDP pode não ser criada automaticamente quando criar o seu VM.
   
   Selecione o seu VM no portal Azure. Clique no botão **Endpoints** para visualizar os pontos finais atualmente configurados para o seu VM. Verifique se existem pontos finais que permitem o tráfego de RDP na porta TCP 3389.
   
   O exemplo que se segue mostra pontos finais válidos que permitem o tráfego de RDP:
   
   ![Verifique os pontos finais dos Serviços cloud no portal Azure](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Se não tiver um ponto final que permita o tráfego de RDP, [crie um ponto final dos Serviços cloud](../windows/classic/setup-endpoints.md). Permitir a TCP para a porta privada 3389.
3. **Reveja**os diagnósticos de arranque vm . Este passo de resolução de problemas analisa os registos da consola VM para determinar se o VM está a reportar um problema. Nem todos os VMs têm diagnósticos de arranque ativados, por isso este passo de resolução de problemas pode ser opcional.
   
    Os passos específicos de resolução de problemas estão fora do âmbito deste artigo, mas podem indicar um problema mais vasto que está a afetar a conectividade rdp. Para obter mais informações sobre a revisão dos registos das consolas e da imagem vM, consulte [Boot Diagnostics para VMs](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).
4. **Verifique a Saúde dos Recursos VM**. Este passo de resolução de problemas verifica que não existem problemas conhecidos com a plataforma Azure que possam ter impacto na conectividade com o VM.
   
    Selecione o seu VM no portal Azure. Desloque-se para baixo do painel de definições para a secção de resolução de **problemas +** perto da parte inferior da lista. Clique no botão Saúde de **Recursos.** Um VM saudável relata como estando **disponível:**
   
    ![Verifique a saúde dos recursos VM no portal Azure](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Redefinir as credenciais de utilizador**. Este passo de resolução de problemas repõe a palavra-passe na conta do administrador local que especifica quando não tem a certeza ou esqueceu as credenciais.  Depois de ter iniciado o início do VM, deve redefinir a palavra-passe para esse utilizador.
   
    Selecione o seu VM no portal Azure. Desloque-se para baixo do painel de definições para a secção de resolução de **problemas +** perto da parte inferior da lista. Clique no botão de **palavra-passe Reset.** Introduza o seu nome de utilizador e uma nova senha. Por fim, clique no botão **Guardar:**
   
    ![Redefinir as credenciais de utilizador no portal Azure](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **Reinicie o seu VM**. Este passo de resolução de problemas pode corrigir quaisquer problemas subjacentes que o próprio VM está a ter.
   
    Selecione o seu VM no portal Azure e clique no separador **'Visão Geral.Clique** no botão **Reiniciar':**
   
    ![Reinicie o VM no portal Azure](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

7. Certifique-se de que qualquer firewall no local, ou firewall no seu computador, permite tráfego de saída TCP 3389 para Azure.

Se ainda estiver a encontrar problemas de PDR, pode [abrir um pedido](https://azure.microsoft.com/support/options/) de apoio ou ler [conceitos e passos mais detalhados do RDP.](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="troubleshoot-specific-rdp-errors"></a>Resolver problemas de erros específicos do RDP
Pode encontrar uma mensagem de erro específica ao tentar ligar-se ao seu VM via RDP. Seguem-se as mensagens de erro mais comuns:

* [A sessão remota foi desligada porque não existem servidores](troubleshoot-specific-rdp-errors.md#rdplicense)de licença de ambiente de trabalho remoto disponíveis para fornecer uma licença .
* [O Ambiente de Trabalho Remoto não consegue encontrar o "nome" do computador.](troubleshoot-specific-rdp-errors.md#rdpname)
* [Ocorreu um erro de autenticação. A Autoridade de Segurança Local não pode ser contactada.](troubleshoot-specific-rdp-errors.md#rdpauth)
* [Erro de Segurança do Windows: As suas credenciais não funcionaram](troubleshoot-specific-rdp-errors.md#wincred).
* [Este computador não pode ligar-se ao computador remoto.](troubleshoot-specific-rdp-errors.md#rdpconnect)

## <a name="additional-resources"></a>Recursos adicionais
Se nenhum destes erros ocorrer e ainda não conseguir ligar-se ao VM via Remote Desktop, leia o guia detalhado de resolução de [problemas para](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)o Ambiente de Trabalho Remoto .
* Para obter medidas de resolução de problemas no acesso a aplicações em execução num VM, consulte o [acesso da Troubleshoot a uma aplicação em execução num Azure VM](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Se estiver a ter problemas com a Secure Shell (SSH) para se ligar a um Linux VM em Azure, consulte [as ligações SSH de Troubleshoot a um Linux VM em Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


