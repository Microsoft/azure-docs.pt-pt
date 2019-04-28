---
title: Não é possível ligar com RDP para uma VM do Windows no Azure | Documentos da Microsoft
description: Resolver problemas quando não é possível ligar à sua máquina virtual do Windows no Azure utilizando o ambiente de trabalho remoto
keywords: Erro de área de trabalho remoto, erro de conexão de área de trabalho remoto, não é possível ligar à VM, resolução de problemas área de trabalho remoto
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 0d740f8e-98b8-4e55-bb02-520f604f5b18
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/23/2018
ms.author: roiyz
ms.openlocfilehash: 50adab1eaa199473a8da857d38c3a08c424c677a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123793"
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Resolver problemas de ligações de ambiente de trabalho remoto para uma máquina virtual do Azure
A ligação RDP (Remote Desktop Protocol) à sua máquina virtual (VM) do Azure baseada em Windows pode falhar por várias razões, impedindo o seu acesso à VM. O problema poderá estar no serviço Ambiente de Trabalho Remoto na VM, na ligação de rede ou no cliente do Ambiente de Trabalho Remoto no computador anfitrião. Este artigo orienta-o através de alguns dos métodos mais comuns para resolver problemas de ligação de RDP. 

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure no [os fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Passos de resolução de problemas rápidos
Após cada passo de resolução de problemas, tente voltar a ligar à VM:

1. Repor configuração de ambiente de trabalho remoto.
2. Grupo de segurança de rede de verificação de regras / na Cloud dos serviços de pontos de extremidade.
3. Reveja os registos da consola VM.
4. Repor o NIC da VM.
5. Verifique o estado de funcionamento do recurso VM.
6. Repor a palavra-passe da VM.
7. Reinicie a VM.
8. Implementar a VM.

Continue a ler se precisar de obter os passos mais detalhados e explicações. Verifique se este equipamento de rede local, tais como routers e firewalls não são bloquear porta de saída TCP 3389, conforme observado na [detalhados de resolução de problemas de cenários RDP](detailed-troubleshoot-rdp.md).

> [!TIP]
> Se o **Connect** botão para a sua VM está a cinzento no portal e não estiver ligado ao Azure via um [Express Route](../../expressroute/expressroute-introduction.md) ou [VPN Site a Site](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) ligação, terá de criar e atribuir um endereço IP público à VM antes de poder utilizar o RDP. Pode ler mais sobre [endereços IP públicos no Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Opções para solucionar problemas RDP
Pode resolver problemas relacionados com as VMs criadas com o modelo de implementação do Resource Manager através de um dos seguintes métodos:

* Portal do Azure – ótimo, se precisar de repor rapidamente as credenciais de utilizador ou de configuração de RDP e não tem as ferramentas do Azure instaladas.
* O Azure PowerShell - se estiver familiarizado com a linha de comandos do PowerShell, repor rapidamente as credenciais de utilizador ou de configuração de RDP utilizando os cmdlets do PowerShell do Azure.

Também pode encontrar passos sobre como resolver as VMs criadas com o [modelo de implementação clássica](#troubleshoot-vms-created-using-the-classic-deployment-model).

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Resolver problemas com o portal do Azure
Após cada passo de resolução de problemas, tente ligar-se novamente à sua VM. Se ainda não conseguir ligar, experimente a próxima etapa.

1. **Repor a sua ligação de RDP**. Este passo de resolução de problemas repõe a configuração de RDP quando ligações remotas estão desativadas ou regras de Firewall do Windows bloqueiam o RDP, por exemplo.
   
    Selecione a sua VM no portal do Azure. Desloque para baixo do painel de definições para o **suporte + resolução de problemas** secção perto da parte inferior da lista. Clique nas **Repor palavra-passe** botão. Definir o **modo** para **repor configuração apenas** e, em seguida, clique nas **atualização** botão:
   
    ![Reposição da configuração de RDP no portal do Azure](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **Regras do grupo de segurança de rede de verificar**. Utilize a [verificação do fluxo de IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) para confirmar se uma regra num Grupo de Segurança de Rede está a bloquear o tráfego de ou para uma máquina virtual. Também pode consultar as regras do grupo de segurança efetivas para se certificar de NSG "Permitir entrada" regra existe e está priorizada para a porta RDP (predefinição 3389). Para obter mais informações, consulte [fluxo de tráfego através de regras de segurança efetivas para resolver problemas da VM](../../virtual-network/diagnose-network-traffic-filter-problem.md).

3. **Consultar diagnósticos de arranque da VM**. Este passo de resolução de problemas analisa os registos de consola da VM para determinar se a VM está a comunicar um problema. Nem todas as VMs têm o diagnóstico de arranque ativado, portanto, este passo de resolução de problemas pode ser opcional.
   
    Passos de resolução de problemas específicos estão além do escopo deste artigo, mas podem indicar um problema mais amplo que está a afetar a conectividade RDP. Para obter mais informações sobre a rever os registos de consola e captura de ecrã da VM, consulte [diagnóstico de arranque para VMs](boot-diagnostics.md).

4. **Repor o NIC da VM**. Para obter mais informações, consulte [como repor o NIC da VM do Windows Azure](../windows/reset-network-interface.md).
5. **Verifique o estado de funcionamento do recurso VM**. Este passo de resolução de problemas verifica se existem não existem problemas conhecidos com a plataforma Azure que possa afetar a conectividade à VM.
   
    Selecione a sua VM no portal do Azure. Desloque para baixo do painel de definições para o **suporte + resolução de problemas** secção perto da parte inferior da lista. Clique nas **estado de funcionamento do recurso** botão. Relatórios de uma VM de bom estado de funcionamento como sendo **disponível**:
   
    ![Verifique o estado de funcionamento de recursos VM no portal do Azure](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **Repor credenciais de utilizador**. Este passo de resolução de problemas repõe a palavra-passe numa conta de administrador local quando não tiver certeza ou ter esquecido as credenciais.  Assim que tiver sessão iniciada na VM, deverá repor a palavra-passe para esse utilizador.
   
    Selecione a sua VM no portal do Azure. Desloque para baixo do painel de definições para o **suporte + resolução de problemas** secção perto da parte inferior da lista. Clique nas **Repor palavra-passe** botão. Certifique-se de que o **modo** está definida como **Repor palavra-passe** e, em seguida, introduza o seu nome de utilizador e uma nova palavra-passe. Por último, clique a **atualização** botão:
   
    ![Repor as credenciais de utilizador no portal do Azure](./media/troubleshoot-rdp-connection/reset-password.png)
7. **Reinicie a VM**. Este passo de resolução de problemas pode corrigir quaisquer problemas subjacentes é fazer com que a própria VM.
   
    Selecione a sua VM no portal do Azure e clique nas **descrição geral** separador. Clique nas **reiniciar** botão:
   
    ![Reinicie a VM no portal do Azure](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **A implementar a VM**. Este passo de resolução de problemas reimplementa sua VM para outro anfitrião no Azure para corrigir problemas de rede ou de qualquer plataforma subjacente.
   
    Selecione a sua VM no portal do Azure. Desloque para baixo do painel de definições para o **suporte + resolução de problemas** secção perto da parte inferior da lista. Clique nas **Reimplementar** botão e, em seguida, clique em **Reimplementar**:
   
    ![Reimplementar a VM no portal do Azure](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    Depois de concluída esta operação, os dados de disco efémero são perdidos e endereços IP dinâmicos que estão associados a VM são atualizados.

9. **Certifique-se de encaminhamento**. Utilizar o observador de rede [do próximo salto](../../network-watcher/network-watcher-check-next-hop-portal.md) capacidade para confirmar que uma rota não está a impedir o tráfego, de que está a ser encaminhados para ou de uma máquina virtual. Também pode rever as rotas efetivas para ver todas as rotas efetivas para uma interface de rede. Para obter mais informações, consulte [fluxo de tráfego de utilizar rotas efetivas para resolver problemas da VM](../../virtual-network/diagnose-network-routing-problem.md).

10. Certifique-se de que qualquer firewall no local, ou de uma firewall no seu computador, permite o tráfego de saída TCP 3389 para o Azure.

Se ainda tiver com problemas RDP, pode [abra um pedido de suporte](https://azure.microsoft.com/support/options/) ou leia [mais detalhadas de conceitos e passos de resolução de problemas de RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-using-azure-powershell"></a>Resolver problemas com o Azure PowerShell
Se ainda não o fez, [instalar e configurar o Azure PowerShell mais recente](/powershell/azure/overview).

Os exemplos seguintes utilizam variáveis como `myResourceGroup`, `myVM`, e `myVMAccessExtension`. Substitua estes nomes de variáveis e localizações de seus próprios valores.

> [!NOTE]
> Repor as credenciais de utilizador e a configuração de RDP através da [Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) cmdlet do PowerShell. Nos exemplos a seguir, `myVMAccessExtension` é um nome que especificar como parte do processo. Se já trabalhou anteriormente com o VMAccessAgent, pode obter o nome da extensão existente com `Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"` para verificar as propriedades da VM. Para ver o nome, procure na secção "Extensões" do resultado.

Após cada passo de resolução de problemas, tente ligar-se novamente à sua VM. Se ainda não conseguir ligar, experimente a próxima etapa.

1. **Repor a sua ligação de RDP**. Este passo de resolução de problemas repõe a configuração de RDP quando ligações remotas estão desativadas ou regras de Firewall do Windows bloqueiam o RDP, por exemplo.
   
    O exemplo seguinte repõe a ligação RDP numa VM com o nome `myVM` no `WestUS` localização e no grupo de recursos com o nome `myResourceGroup`:
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **Regras do grupo de segurança de rede de verificar**. Este passo de resolução de problemas verifica que tem uma regra no seu grupo de segurança de rede para permitir o tráfego RDP. A porta predefinida para RDP é a porta TCP 3389. Uma regra para permitir tráfego RDP pode não ser criada automaticamente quando criar a sua VM.
   
    Em primeiro lugar, atribuir todos os dados de configuração para o seu grupo de segurança de rede para o `$rules` variável. O exemplo seguinte obtém informações sobre o grupo de segurança de rede com o nome `myNetworkSecurityGroup` no grupo de recursos com o nome `myResourceGroup`:
   
    ```powershell
    $rules = Get-AzNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    Agora, veja as regras que estão configuradas para este grupo de segurança de rede. Certifique-se de que existe uma regra para permitir que a porta TCP 3389 para ligações de entrada da seguinte forma:
   
    ```powershell
    $rules.SecurityRules
    ```
   
    O exemplo seguinte mostra uma regra de segurança válido que permite tráfego RDP. Pode ver `Protocol`, `DestinationPortRange`, `Access`, e `Direction` estão configuradas corretamente:
   
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
   
    Se não tiver uma regra que permita o tráfego RDP [criar uma regra de grupo de segurança de rede](../windows/nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Permitir que a porta TCP 3389.
3. **Repor credenciais de utilizador**. Este passo de resolução de problemas repõe a palavra-passe da conta de administrador local que especificou quando não sabe ou ter esquecido, as credenciais.
   
    Em primeiro lugar, especifique o nome de utilizador e uma nova palavra-passe através da atribuição de credenciais para o `$cred` variável da seguinte forma:
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Agora, Atualize as credenciais na sua VM. O exemplo seguinte atualiza as credenciais numa VM com o nome `myVM` no `WestUS` localização e no grupo de recursos com o nome `myResourceGroup`:
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Reinicie a VM**. Este passo de resolução de problemas pode corrigir quaisquer problemas subjacentes é fazer com que a própria VM.
   
    O seguinte exemplo reinicia a VM com o nome `myVM` no grupo de recursos com o nome `myResourceGroup`:
   
    ```powershell
    Restart-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **A implementar a VM**. Este passo de resolução de problemas reimplementa sua VM para outro anfitrião no Azure para corrigir problemas de rede ou de qualquer plataforma subjacente.
   
    O exemplo seguinte reimplementa a VM com o nome `myVM` no `WestUS` localização e no grupo de recursos com o nome `myResourceGroup`:
   
    ```powershell
    Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

6. **Certifique-se de encaminhamento**. Utilizar o observador de rede [do próximo salto](../../network-watcher/network-watcher-check-next-hop-portal.md) capacidade para confirmar que uma rota não está a impedir o tráfego, de que está a ser encaminhados para ou de uma máquina virtual. Também pode rever as rotas efetivas para ver todas as rotas efetivas para uma interface de rede. Para obter mais informações, consulte [fluxo de tráfego de utilizar rotas efetivas para resolver problemas da VM](../../virtual-network/diagnose-network-routing-problem.md).

7. Certifique-se de que qualquer firewall no local, ou de uma firewall no seu computador, permite o tráfego de saída TCP 3389 para o Azure.

Se ainda tiver com problemas RDP, pode [abra um pedido de suporte](https://azure.microsoft.com/support/options/) ou leia [mais detalhadas de conceitos e passos de resolução de problemas de RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Resolver problemas relacionados com as VMs criadas com o modelo de implementação clássica
Após cada passo de resolução de problemas, tente voltar a ligar à VM.

1. **Repor a sua ligação de RDP**. Este passo de resolução de problemas repõe a configuração de RDP quando ligações remotas estão desativadas ou regras de Firewall do Windows bloqueiam o RDP, por exemplo.
   
    Selecione a sua VM no portal do Azure. Clique no **... Obter mais** botão, em seguida, clique em **repor o acesso remoto**:
   
    ![Reposição da configuração de RDP no portal do Azure](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Certifique-se de pontos de extremidade de serviços Cloud**. Este passo de resolução de problemas verifica que tem pontos de extremidade nos serviços Cloud para permitir o tráfego RDP. A porta predefinida para RDP é a porta TCP 3389. Uma regra para permitir tráfego RDP pode não ser criada automaticamente quando criar a sua VM.
   
   Selecione a sua VM no portal do Azure. Clique nas **pontos de extremidade** botão para ver os pontos finais atualmente configurados para a sua VM. Certifique-se de que existem pontos de extremidade que permitam o tráfego RDP na porta TCP 3389.
   
   O exemplo seguinte mostra os pontos de extremidade válidos, que permitem o tráfego RDP:
   
   ![Certifique-se de pontos de extremidade de serviços Cloud no portal do Azure](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Se não tiver um ponto de extremidade que permita o tráfego RDP, [criar um ponto de extremidade de serviços Cloud](../windows/classic/setup-endpoints.md). Permita o TCP para a porta privada 3389.
3. **Consultar diagnósticos de arranque da VM**. Este passo de resolução de problemas analisa os registos de consola da VM para determinar se a VM está a comunicar um problema. Nem todas as VMs têm o diagnóstico de arranque ativado, portanto, este passo de resolução de problemas pode ser opcional.
   
    Passos de resolução de problemas específicos estão além do escopo deste artigo, mas podem indicar um problema mais amplo que está a afetar a conectividade RDP. Para obter mais informações sobre a rever os registos de consola e captura de ecrã da VM, consulte [diagnóstico de arranque para VMs](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).
4. **Verifique o estado de funcionamento do recurso VM**. Este passo de resolução de problemas verifica se existem não existem problemas conhecidos com a plataforma Azure que possa afetar a conectividade à VM.
   
    Selecione a sua VM no portal do Azure. Desloque para baixo do painel de definições para o **suporte + resolução de problemas** secção perto da parte inferior da lista. Clique nas **Resource Health** botão. Relatórios de uma VM de bom estado de funcionamento como sendo **disponível**:
   
    ![Verifique o estado de funcionamento de recursos VM no portal do Azure](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Repor credenciais de utilizador**. Este passo de resolução de problemas repõe a palavra-passe da conta de administrador local que especificou quando não tiver certeza ou ter esquecido as credenciais.  Assim que tiver sessão iniciada na VM, deverá repor a palavra-passe para esse utilizador.
   
    Selecione a sua VM no portal do Azure. Desloque para baixo do painel de definições para o **suporte + resolução de problemas** secção perto da parte inferior da lista. Clique nas **Repor palavra-passe** botão. Introduza o nome de utilizador e uma nova palavra-passe. Por último, clique a **guardar** botão:
   
    ![Repor as credenciais de utilizador no portal do Azure](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **Reinicie a VM**. Este passo de resolução de problemas pode corrigir quaisquer problemas subjacentes é fazer com que a própria VM.
   
    Selecione a sua VM no portal do Azure e clique nas **descrição geral** separador. Clique nas **reiniciar** botão:
   
    ![Reinicie a VM no portal do Azure](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

7. Certifique-se de que qualquer firewall no local, ou de uma firewall no seu computador, permite o tráfego de saída TCP 3389 para o Azure.

Se ainda tiver com problemas RDP, pode [abra um pedido de suporte](https://azure.microsoft.com/support/options/) ou leia [mais detalhadas de conceitos e passos de resolução de problemas de RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-specific-rdp-errors"></a>Resolver problemas de erros específicos de RDP
Pode encontrar uma mensagem de erro específico ao tentar ligar à VM através de RDP. Seguem-se as mensagens de erro mais comuns:

* [A sessão remota foi desligada porque existem não existem servidores de licença de ambiente de trabalho remoto disponíveis para fornecer uma licença](troubleshoot-specific-rdp-errors.md#rdplicense).
* [Ambiente de trabalho remoto não é possível localizar o computador "name"](troubleshoot-specific-rdp-errors.md#rdpname).
* [Ocorreu um erro de autenticação. Não é possível contactar a autoridade de segurança Local](troubleshoot-specific-rdp-errors.md#rdpauth).
* [Erro de segurança do Windows: As suas credenciais não funcionavam](troubleshoot-specific-rdp-errors.md#wincred).
* [Este computador não é possível ligar ao computador remoto](troubleshoot-specific-rdp-errors.md#rdpconnect).

## <a name="additional-resources"></a>Recursos adicionais
Se nenhum destes erros ocorreram e ainda não é possível ligar à VM através do ambiente de trabalho remoto, leia o detalhadas [guia para o ambiente de trabalho remoto de resolução de problemas](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para passos ao aceder ao aplicações em execução numa VM de resolução de problemas, consulte [resolver problemas de acesso para uma aplicação em execução numa VM do Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Se estiver a ter problemas com o Secure Shell (SSH) para ligar a uma VM do Linux no Azure, consulte [resolver problemas de SSH ligações a uma VM do Linux no Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


