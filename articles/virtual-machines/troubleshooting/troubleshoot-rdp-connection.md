---
title: Não é possível ligar-se com o RDP a um VM do Windows em Azure Microsoft Docs
description: Problemas de resolução de problemas quando não consegue ligar-se à sua máquina virtual Do Windows no Azure utilizando o Remote Desktop
keywords: Erro remoto no ambiente de trabalho,erro de ligação ao ambiente de trabalho remoto, não pode ligar-se a VM,resolução remota de problemas no ambiente de trabalho
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
ms.openlocfilehash: ba6dda86475456b6797d27e11727d70261be2e1a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91439994"
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Resolução de problemas Ligações de ambiente de trabalho remoto a uma máquina virtual Azure
A ligação RDP (Remote Desktop Protocol) à sua máquina virtual (VM) do Azure baseada em Windows pode falhar por várias razões, impedindo o seu acesso à VM. O problema poderá estar no serviço Ambiente de Trabalho Remoto na VM, na ligação de rede ou no cliente do Ambiente de Trabalho Remoto no computador anfitrião. Este artigo serve de orientação ao longo de alguns dos métodos mais comuns para resolver problemas de ligação RDP. 

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter **Apoio**.

 

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Passos rápidos de resolução de problemas
Após cada passo de resolução de problemas, tente voltar a ligar-se ao VM:

1. Reinicie a configuração do ambiente de trabalho remoto.
2. Verifique as regras do Grupo de Segurança da Rede / Pontos finais dos Serviços de Nuvem.
3. Rever registos de consolas VM.
4. Repor o NIC para o VM.
5. Verifique a Saúde dos Recursos VM.
6. Reinicie a sua senha de VM.
7. Reinicie o seu VM.
8. Reimplantar o seu VM.

Continuar a ler Se precisar de passos e explicações mais detalhados. Verifique se os equipamentos de rede locais, tais como routers e firewalls, não estão a bloquear a porta TCP de saída 3389, como se nota em [cenários detalhados de resolução de problemas rdp](detailed-troubleshoot-rdp.md).

> [!TIP]
> Se o botão **'Ligar'** para o seu VM estiver acinzentado no portal e não estiver ligado ao Azure através de uma [rota expressa](../../expressroute/expressroute-introduction.md) ou de uma ligação [VPN local-a-local,](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) tem de criar e atribuir ao seu VM um endereço IP público antes de poder utilizar o RDP. Pode ler mais sobre [endereços IP públicos no Azure](../../virtual-network/public-ip-addresses.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Formas de resolver problemas de PDR
Pode resolver problemas vMs criados utilizando o modelo de implementação do Gestor de Recursos utilizando um dos seguintes métodos:

* Portal Azure - ótimo se precisar de redefinir rapidamente a configuração RDP ou as credenciais do utilizador e não tiver as ferramentas Azure instaladas.
* Azure PowerShell - se estiver confortável com uma pressão PowerShell, reinicie rapidamente a configuração RDP ou as credenciais do utilizador utilizando os cmdlets Azure PowerShell.

Também pode encontrar passos na resolução de VMs criados com o [modelo de implementação Classic.](#troubleshoot-vms-created-using-the-classic-deployment-model)

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Resolução de problemas usando o portal Azure
Depois de cada passo de resolução de problemas, tente ligar-se novamente ao seu VM. Se ainda não conseguir ligar, tente o próximo passo.

1. **Reinicie a sua ligação RDP**. Este passo de resolução de problemas reinicia a configuração RDP quando as ligações remotas são desativadas ou as regras do Windows Firewall estão a bloquear o RDP, por exemplo.
   
    Selecione o seu VM no portal Azure. Desloque o painel de definições para a secção **de resolução de problemas De suporte + resolução** de problemas perto da parte inferior da lista. Clique no botão **de palavra-passe Reset.** Desajuste o **modo** **apenas** para redefinir a configuração e, em seguida, clique no botão **'Atualização':**
   
    ![Reinicie a configuração de R D P no portal Azure.](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **Verifique as regras do Grupo de Segurança da Rede**. Utilize [o fluxo IP para](../../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) confirmar se uma regra de um Grupo de Segurança de Rede está a bloquear o tráfego de ou para uma máquina virtual. Também pode rever as regras eficazes do grupo de segurança para garantir que a regra NSG de entrada existe e é prioritária para a porta RDP (padrão 3389). Para obter mais informações, consulte [a utilização de regras de segurança eficazes para resolver o fluxo de tráfego de VM.](../../virtual-network/diagnose-network-traffic-filter-problem.md)

3. **Rever diagnósticos de arranque em VM**. Este passo de resolução de problemas revê os registos de consolas VM para determinar se o VM está a reportar um problema. Nem todos os VMs têm diagnósticos de arranque ativados, por isso este passo de resolução de problemas pode ser opcional.
   
    As etapas específicas de resolução de problemas estão fora do âmbito deste artigo, mas podem indicar um problema mais amplo que está a afetar a conectividade RDP. Para obter mais informações sobre a revisão dos registos das consolas e da imagem de VM, consulte [o Boot Diagnostics for VMs](boot-diagnostics.md).

4. **Repor o NIC para o VM**. Para obter mais informações, consulte [como redefinir o NIC para OZure Windows VM](./reset-network-interface.md).
5. **Verifique a Saúde dos Recursos VM.** Este passo de resolução de problemas verifica que não existem problemas conhecidos com a plataforma Azure que possam ter impacto na conectividade com o VM.
   
    Selecione o seu VM no portal Azure. Desloque o painel de definições para a secção **de resolução de problemas De suporte + resolução** de problemas perto da parte inferior da lista. Clique no botão **de saúde recursos.** Um VM saudável relata como **estando disponível:**
   
    ![Verifique a saúde dos recursos V M no portal Azure.](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **Redefinir as credenciais do utilizador**. Este passo de resolução de problemas reinicia a palavra-passe numa conta de administrador local quando não tem a certeza ou esqueceu-se das credenciais.  Uma vez iniciada a sessão no VM, deverá redefinir a palavra-passe para esse utilizador.
   
    Selecione o seu VM no portal Azure. Desloque o painel de definições para a secção **de resolução de problemas De suporte + resolução** de problemas perto da parte inferior da lista. Clique no botão **de palavra-passe Reset.** Certifique-se de que o **Modo** está definido para redefinir a **palavra-passe** e, em seguida, insira o seu nome de utilizador e uma nova palavra-passe. Por fim, clique no botão **'Atualização':**
   
    ![Repor as credenciais de utilizador no portal Azure.](./media/troubleshoot-rdp-connection/reset-password.png)
7. **Reinicie o seu VM**. Este passo de resolução de problemas pode corrigir quaisquer problemas subjacentes que o próprio VM está a ter.
   
    Selecione o seu VM no portal Azure e clique no **separador Vista Geral.** Clique no botão **Reiniciar:**
   
    ![Reinicie o V M no portal Azure.](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **Reimplantar o seu VM**. Este passo de resolução de problemas recoloca o seu VM em outro hospedeiro dentro do Azure para corrigir quaisquer problemas subjacentes à plataforma ou networking.
   
    Selecione o seu VM no portal Azure. Desloque o painel de definições para a secção **de resolução de problemas De suporte + resolução** de problemas perto da parte inferior da lista. Clique no botão **Recolocação** e, em seguida, clique em **Recolocar**:
   
    ![Reimplantar o VM no portal Azure](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    Após o fim desta operação, perdem-se dados efémeros de disco e atualizam-se os endereços IP dinâmicos associados ao VM.

9. **Verifique o encaminhamento**. Utilize a capacidade de [lúpulo next](../../network-watcher/diagnose-vm-network-routing-problem.md) do Network Watcher para confirmar que uma rota não impede que o tráfego seja encaminhado para ou de uma máquina virtual. Também pode rever rotas eficazes para ver todas as rotas eficazes para uma interface de rede. Para obter mais informações, consulte [utilizando rotas eficazes para resolver o fluxo de tráfego de VM.](../../virtual-network/diagnose-network-routing-problem.md)

10. Certifique-se de que qualquer firewall no local, ou firewall no seu computador, permite o tráfego de saída TCP 3389 para Azure.

Se ainda estiver a encontrar problemas de PDR, pode [abrir um pedido de apoio](https://azure.microsoft.com/support/options/) ou ler [conceitos e passos mais detalhados de resolução de problemas do PDR.](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="troubleshoot-using-azure-powershell"></a>Resolver problemas com o Azure PowerShell
Se ainda não o fez, [instale e configuure o mais recente Azure PowerShell](/powershell/azure/).

Os exemplos que se seguem utilizam variáveis `myResourceGroup` `myVM` como, e `myVMAccessExtension` . Substitua estes nomes e locais variáveis por seus próprios valores.

> [!NOTE]
> Repõe as credenciais do utilizador e a configuração RDP utilizando o cmdlet PowerShell de [Set-AzVMAccessExtension.](/powershell/module/az.compute/set-azvmaccessextension) Nos exemplos a seguir, `myVMAccessExtension` é um nome que especifica como parte do processo. Se já trabalhou com o VMAccessAgent, pode obter o nome da extensão existente utilizando `Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"` para verificar as propriedades do VM. Para ver o nome, consulte a secção 'Extensões' da saída.

Depois de cada passo de resolução de problemas, tente ligar-se novamente ao seu VM. Se ainda não conseguir ligar, tente o próximo passo.

1. **Reinicie a sua ligação RDP**. Este passo de resolução de problemas reinicia a configuração RDP quando as ligações remotas são desativadas ou as regras do Windows Firewall estão a bloquear o RDP, por exemplo.
   
    O exemplo seguinte repõe a ligação RDP num VM nomeado `myVM` no local e no grupo de recursos `WestUS` `myResourceGroup` denominado:
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **Verifique as regras do Grupo de Segurança da Rede**. Este passo de resolução de problemas verifica que tem uma regra no seu Grupo de Segurança de Rede para permitir o tráfego rdp. A porta predefinida para RDP é a porta TCP 3389. Uma regra para permitir o tráfego RDP não pode ser criada automaticamente quando criar o seu VM.
   
    Em primeiro lugar, atribua todos os dados de configuração do seu Grupo de Segurança de Rede à `$rules` variável. O exemplo a seguir obtém informações sobre o Grupo de Segurança da Rede nomeado `myNetworkSecurityGroup` no grupo de recursos `myResourceGroup` denominado:
   
    ```powershell
    $rules = Get-AzNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    Agora, veja as regras que estão configuradas para este Grupo de Segurança de Rede. Verifique se existe uma regra que permite a porta TCP 3389 para ligações de entrada da seguinte forma:
   
    ```powershell
    $rules.SecurityRules
    ```
   
    O exemplo a seguir mostra uma regra de segurança válida que permite o tráfego rdp. Pode `Protocol` `DestinationPortRange` ver, e `Access` está `Direction` configurado corretamente:
   
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
   
    Se não tiver uma regra que permita o tráfego RDP, [crie uma regra do Grupo de Segurança de Rede](../windows/nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Permitir a porta TCP 3389.
3. **Redefinir as credenciais do utilizador**. Este passo de resolução de problemas reinicia a palavra-passe na conta do administrador local que especifica quando não tem a certeza ou se esqueceu das credenciais.
   
    Em primeiro lugar, especifique o nome de utilizador e uma nova palavra-passe atribuindo credenciais à `$cred` variável da seguinte forma:
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Agora, atualize as credenciais no seu VM. O exemplo a seguir atualiza as credenciais de um VM nomeado `myVM` no local e no grupo de recursos `WestUS` `myResourceGroup` denominado:
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Reinicie o seu VM**. Este passo de resolução de problemas pode corrigir quaisquer problemas subjacentes que o próprio VM está a ter.
   
    O exemplo a seguir reinicia o VM nomeado `myVM` no grupo de recursos `myResourceGroup` denominado:
   
    ```powershell
    Restart-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **Reimplantar o seu VM**. Este passo de resolução de problemas recoloca o seu VM em outro hospedeiro dentro do Azure para corrigir quaisquer problemas subjacentes à plataforma ou networking.
   
    O exemplo a seguir redistribui o VM nomeado `myVM` no local e no grupo de recursos `WestUS` `myResourceGroup` denominado:
   
    ```powershell
    Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

6. **Verifique o encaminhamento**. Utilize a capacidade de [lúpulo next](../../network-watcher/diagnose-vm-network-routing-problem.md) do Network Watcher para confirmar que uma rota não impede que o tráfego seja encaminhado para ou de uma máquina virtual. Também pode rever rotas eficazes para ver todas as rotas eficazes para uma interface de rede. Para obter mais informações, consulte [utilizando rotas eficazes para resolver o fluxo de tráfego de VM.](../../virtual-network/diagnose-network-routing-problem.md)

7. Certifique-se de que qualquer firewall no local, ou firewall no seu computador, permite o tráfego de saída TCP 3389 para Azure.

Se ainda estiver a encontrar problemas de PDR, pode [abrir um pedido de apoio](https://azure.microsoft.com/support/options/) ou ler [conceitos e passos mais detalhados de resolução de problemas do PDR.](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>VMs de resolução de problemas criados usando o modelo de implementação Clássico

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


Após cada passo de resolução de problemas, tente voltar a ligar-se ao VM.

1. **Reinicie a sua ligação RDP**. Este passo de resolução de problemas reinicia a configuração RDP quando as ligações remotas são desativadas ou as regras do Windows Firewall estão a bloquear o RDP, por exemplo.
   
    Selecione o seu VM no portal Azure. Clique no **... Mais** botão e, em seguida, clique **em Reiniciar o Acesso Remoto**:
   
    ![Reinicie a configuração R D P no portal Azure para V M utilizando um modelo de implantação clássico.](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Verifique os pontos finais dos Serviços cloud**. Este passo de resolução de problemas verifica que tem pontos finais nos seus Serviços cloud para permitir o tráfego de RDP. A porta predefinida para RDP é a porta TCP 3389. Uma regra para permitir o tráfego RDP não pode ser criada automaticamente quando criar o seu VM.
   
   Selecione o seu VM no portal Azure. Clique no botão **Pontos finais** para ver os pontos finais atualmente configurados para o seu VM. Verifique se existem pontos finais que permitem o tráfego de PDR na porta TCP 3389.
   
   O exemplo a seguir mostra pontos finais válidos que permitem o tráfego de PDR:
   
   ![Verifique os pontos finais dos Serviços cloud no portal Azure para V M utilizando o modelo de implementação clássico.](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Se não tiver um ponto final que permita o tráfego RDP, [crie um ponto final cloud Services](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints). Permitir a TCP para o porto privado 3389.
3. **Rever diagnósticos de arranque em VM**. Este passo de resolução de problemas revê os registos de consolas VM para determinar se o VM está a reportar um problema. Nem todos os VMs têm diagnósticos de arranque ativados, por isso este passo de resolução de problemas pode ser opcional.
   
    As etapas específicas de resolução de problemas estão fora do âmbito deste artigo, mas podem indicar um problema mais amplo que está a afetar a conectividade RDP. Para obter mais informações sobre a revisão dos registos das consolas e da imagem de VM, consulte [o Boot Diagnostics for VMs](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).
4. **Verifique a Saúde dos Recursos VM.** Este passo de resolução de problemas verifica que não existem problemas conhecidos com a plataforma Azure que possam ter impacto na conectividade com o VM.
   
    Selecione o seu VM no portal Azure. Desloque o painel de definições para a secção **de resolução de problemas De suporte + resolução** de problemas perto da parte inferior da lista. Clique no botão **Resource Health.** Um VM saudável relata como **estando disponível:**
   
    ![Verifique a saúde dos recursos V M no portal Azure para V M utilizando um modelo de implementação clássico.](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Redefinir as credenciais do utilizador**. Este passo de resolução de problemas reinicia a palavra-passe na conta do administrador local que especifica quando não tem a certeza ou se esqueceu das credenciais.  Uma vez iniciada a sessão no VM, deverá redefinir a palavra-passe para esse utilizador.
   
    Selecione o seu VM no portal Azure. Desloque o painel de definições para a secção **de resolução de problemas De suporte + resolução** de problemas perto da parte inferior da lista. Clique no botão **de palavra-passe Reset.** Insira o seu nome de utilizador e uma nova senha. Finalmente, clique no botão **Guardar:**
   
    ![Repor as credenciais do utilizador no portal Azure para V M utilizando um modelo de implementação clássico.](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **Reinicie o seu VM**. Este passo de resolução de problemas pode corrigir quaisquer problemas subjacentes que o próprio VM está a ter.
   
    Selecione o seu VM no portal Azure e clique no **separador Vista Geral.** Clique no botão **Reiniciar:**
   
    ![Reinicie o V M no portal Azure para V M utilizando um modelo de implantação clássico.](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

7. Certifique-se de que qualquer firewall no local, ou firewall no seu computador, permite o tráfego de saída TCP 3389 para Azure.

Se ainda estiver a encontrar problemas de PDR, pode [abrir um pedido de apoio](https://azure.microsoft.com/support/options/) ou ler [conceitos e passos mais detalhados de resolução de problemas do PDR.](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="troubleshoot-specific-rdp-errors"></a>Resolver problemas de erros específicos do RDP
Pode encontrar uma mensagem de erro específica ao tentar ligar-se ao seu VM via RDP. Seguem-se as mensagens de erro mais comuns:

* [A sessão remota foi desligada porque não existem servidores de licença de secretária remoto disponíveis para fornecer uma licença](troubleshoot-specific-rdp-errors.md#rdplicense).
* [O Ambiente de Trabalho Remoto não consegue encontrar o "nome" do computador.](troubleshoot-specific-rdp-errors.md#rdpname)
* [Ocorreu um erro de autenticação. A Autoridade de Segurança Local não pode ser contactada.](troubleshoot-specific-rdp-errors.md#rdpauth)
* [Erro de segurança do Windows: As suas credenciais não funcionaram](troubleshoot-specific-rdp-errors.md#wincred).
* [Este computador não pode ligar-se ao computador remoto.](troubleshoot-specific-rdp-errors.md#rdpconnect)

## <a name="additional-resources"></a>Recursos adicionais
Se nenhum destes erros ocorrer e ainda não conseguir ligar-se ao VM via Remote Desktop, leia o guia de resolução de [problemas](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)detalhado para o Ambiente de Trabalho Remoto .
* Para obter etapas de resolução de problemas no acesso a aplicações em execução num VM, consulte [o acesso de Resolução de Problemas a uma aplicação em execução num VM Azure](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json).
* Se tiver problemas em utilizar a Secure Shell (SSH) para ligar a um Linux VM em Azure, consulte [as ligações SSH de resolução de problemas a um Linux VM em Azure](./troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json).
