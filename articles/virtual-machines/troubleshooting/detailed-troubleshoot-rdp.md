---
title: Solução de problemas detalhada da área de trabalho remota no Azure | Microsoft Docs
description: Examine as etapas detalhadas de solução de problemas para erros de área de trabalho remota em que não é possível fazer uma máquina virtual do Windows no Azure
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: Não é possível se conectar à área de trabalho remota, solucionar problemas na área de trabalho remota, a área de trabalho remota não pode se conectar, erros de área de trabalho remota, solução de problemas
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: ee2fb3757b0e3a7015a98f4e04084fd9c6a4850d
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75747539"
---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Etapas de solução de problemas detalhadas para problemas de conexão de área de trabalho remota para VMs do Windows no Azure
Este artigo fornece etapas de solução de problemas detalhadas para diagnosticar e corrigir erros complexos de Área de Trabalho Remota para máquinas virtuais do Azure baseadas no Windows.

> [!IMPORTANT]
> Para eliminar os erros de Área de Trabalho Remota mais comuns, leia [o artigo básico de solução de problemas para área de trabalho remota](troubleshoot-rdp-connection.md) antes de continuar.

Você pode encontrar um Área de Trabalho Remota mensagem de erro que não se assemelha a nenhuma das mensagens de erro específicas abordadas no [Guia de solução de problemas do área de trabalho remota básico](troubleshoot-rdp-connection.md). Siga estas etapas para determinar por que o cliente do Área de Trabalho Remota (RDP) não consegue se conectar ao serviço RDP na VM do Azure.


Se precisar de mais ajuda a qualquer momento neste artigo, você poderá entrar em contato com os especialistas do Azure no [msdn do Azure e nos fóruns de Stack Overflow](https://azure.microsoft.com/support/forums/). Como alternativa, você também pode arquivar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **obter suporte**. Para obter informações sobre como usar o suporte do Azure, leia as [perguntas frequentes sobre suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="components-of-a-remote-desktop-connection"></a>Componentes de uma conexão Área de Trabalho Remota
Os seguintes componentes estão envolvidos em uma conexão RDP:

![](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

Antes de continuar, talvez seja útil revisar mentalmente o que mudou desde a última conexão com êxito Área de Trabalho Remota com a VM. Por exemplo:

* O endereço IP público da VM ou o serviço de nuvem que contém a VM (também chamado de [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)de endereço IP virtual) foi alterado. A falha de RDP pode ser porque o cache do cliente DNS ainda tem o *endereço IP antigo* registrado para o nome DNS. Libere o cache do cliente DNS e tente conectar a VM novamente. Ou tente se conectar diretamente com o novo VIP.
* Você está usando um aplicativo de terceiros para gerenciar suas conexões de Área de Trabalho Remota em vez de usar a conexão gerada pelo portal do Azure. Verifique se a configuração do aplicativo inclui a porta TCP correta para o tráfego de Área de Trabalho Remota. Você pode verificar essa porta para uma máquina virtual clássica na [portal do Azure](https://portal.azure.com), clicando nas configurações da VM > pontos de extremidade.

## <a name="preliminary-steps"></a>Etapas preliminares
Antes de prosseguir para a solução de problemas detalhada,

* Verifique o status da máquina virtual na portal do Azure para quaisquer problemas óbvios.
* Siga as [etapas de correção rápida para erros comuns de RDP no guia de solução de problemas básico](troubleshoot-rdp-connection.md#quick-troubleshooting-steps).
* Para imagens personalizadas, certifique-se de que o VHD está adequadamente preparado antes de carregá-lo. Para obter mais informações, consulte [preparar um VHD do Windows ou VHDX para carregar no Azure](../windows/prepare-for-upload-vhd-image.md).


Tente reconectar-se à VM por meio de Área de Trabalho Remota após essas etapas.

## <a name="detailed-troubleshooting-steps"></a>Passos detalhados de resolução de problemas
O cliente Área de Trabalho Remota pode não conseguir acessar o serviço de Área de Trabalho Remota na VM do Azure devido a problemas nas seguintes fontes:

* [Área de Trabalho Remota computador cliente](#source-1-remote-desktop-client-computer)
* [Dispositivo de borda de intranet da organização](#source-2-organization-intranet-edge-device)
* [Ponto de extremidade do serviço de nuvem e lista de controle de acesso (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Grupos de segurança de rede](#source-4-network-security-groups)
* [VM do Azure baseada no Windows](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>Fonte 1: Área de Trabalho Remota computador cliente
Verifique se o computador pode fazer Área de Trabalho Remota conexões com outro computador local baseado no Windows.

![](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

Se não puder, verifique as seguintes configurações no seu computador:

* Uma configuração de firewall local que está bloqueando o tráfego de Área de Trabalho Remota.
* Software proxy de cliente instalado localmente que está impedindo conexões Área de Trabalho Remota.
* Software de monitoramento de rede instalado localmente que está impedindo conexões Área de Trabalho Remota.
* Outros tipos de software de segurança que monitoram o tráfego ou permitem/proíbem tipos específicos de tráfego que está impedindo conexões Área de Trabalho Remota.

Em todos esses casos, Desabilite temporariamente o software e tente se conectar a um computador local por meio de Área de Trabalho Remota. Se você puder descobrir a causa real dessa maneira, trabalhe com o administrador da rede para corrigir as configurações de software para permitir conexões de Área de Trabalho Remota.

## <a name="source-2-organization-intranet-edge-device"></a>Fonte 2: dispositivo de borda de intranet da organização
Verifique se um computador conectado diretamente à Internet pode fazer conexões Área de Trabalho Remota à sua máquina virtual do Azure.

![](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

Se você não tiver um computador conectado diretamente à Internet, crie e teste com uma nova máquina virtual do Azure em um grupo de recursos ou serviço de nuvem. Para obter mais informações, consulte [criar uma máquina virtual executando o Windows no Azure](../virtual-machines-windows-hero-tutorial.md). Você pode excluir a máquina virtual e o grupo de recursos ou o serviço de nuvem, após o teste.

Se você puder criar uma conexão Área de Trabalho Remota com um computador conectado diretamente à Internet, verifique o dispositivo de borda de intranet da sua organização para:

* Um firewall interno que bloqueia conexões HTTPS com a Internet.
* Um servidor proxy que impede conexões Área de Trabalho Remota.
* Software de detecção de intrusão ou monitoramento de rede em execução em dispositivos na sua rede de borda que está impedindo conexões Área de Trabalho Remota.

Trabalhe com o administrador de rede para corrigir as configurações do dispositivo de borda de intranet da sua organização para permitir conexões de Área de Trabalho Remota baseadas em HTTPS com a Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Origem 3: ponto de extremidade de serviço de nuvem e ACL
Para VMs criadas usando o modelo de implantação clássico, verifique se outra VM do Azure que está no mesmo serviço de nuvem ou rede virtual pode fazer conexões de Área de Trabalho Remota com sua VM do Azure.

![](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> Para máquinas virtuais criadas no Gerenciador de recursos, pule para [fonte 4: grupos de segurança de rede](#source-4-network-security-groups).

Se você não tiver outra máquina virtual no mesmo serviço de nuvem ou rede virtual, crie uma. Siga as etapas em [criar uma máquina virtual executando o Windows no Azure](../virtual-machines-windows-hero-tutorial.md). Exclua a máquina virtual de teste após a conclusão do teste.

Se você puder se conectar via Área de Trabalho Remota a uma máquina virtual no mesmo serviço de nuvem ou rede virtual, verifique se há estas configurações:

* A configuração do ponto de extremidade para Área de Trabalho Remota tráfego na VM de destino: a porta TCP privada do ponto de extremidade deve corresponder à porta TCP na qual o serviço Área de Trabalho Remota da VM está escutando (o padrão é 3389).
* A ACL para o ponto de extremidade de tráfego de Área de Trabalho Remota na VM de destino: as ACLs permitem que você especifique o tráfego de entrada permitido ou negado da Internet com base em seu endereço IP de origem. ACLs configuradas incorretamente podem impedir o tráfego de Área de Trabalho Remota de entrada para o ponto de extremidade. Verifique suas ACLs para garantir que o tráfego de entrada dos endereços IP públicos de seu proxy ou de outro servidor de borda seja permitido. Para obter mais informações, consulte [o que é uma ACL (lista de controle de acesso) de rede?](../../virtual-network/virtual-networks-acl.md)

Para verificar se o ponto de extremidade é a origem do problema, remova o ponto de extremidade atual e crie um novo, escolhendo uma porta aleatória no intervalo de 49152 – 65535 para o número da porta externa. Para obter mais informações, consulte [como configurar pontos de extremidade para uma máquina virtual](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="source-4-network-security-groups"></a>Fonte 4: grupos de segurança de rede
Os grupos de segurança de rede permitem um controle mais granular do tráfego de entrada e de saída permitido. Você pode criar regras que abrangem sub-redes e serviços de nuvem em uma rede virtual do Azure.

Utilize a [verificação do fluxo de IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) para confirmar se uma regra num Grupo de Segurança de Rede está a bloquear o tráfego de ou para uma máquina virtual. Você também pode examinar as regras de grupo de segurança efetivas para garantir que a regra "permitir" NSG de entrada exista e seja priorizada para a porta RDP (padrão 3389). Para obter mais informações, consulte [usando regras de segurança efetivas para solucionar problemas de fluxo de tráfego de VM](../../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="source-5-windows-based-azure-vm"></a>Fonte 5: VM do Azure baseada no Windows
![](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

Siga as instruções neste [artigo](../windows/reset-rdp.md). Este artigo redefine o serviço de Área de Trabalho Remota na máquina virtual:

* Habilite a regra padrão do firewall do Windows "Área de Trabalho Remota" (porta TCP 3389).
* Habilite conexões Área de Trabalho Remota definindo o valor do registro HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections como 0.

Tente a conexão do computador novamente. Se você ainda não conseguir se conectar via Área de Trabalho Remota, verifique os seguintes problemas possíveis:

* O serviço de Área de Trabalho Remota não está em execução na VM de destino.
* O serviço de Área de Trabalho Remota não está escutando na porta TCP 3389.
* O Firewall do Windows ou outro firewall local tem uma regra de saída que está impedindo o tráfego de Área de Trabalho Remota.
* O software de detecção de intrusão ou de monitoramento de rede em execução na máquina virtual do Azure está impedindo conexões Área de Trabalho Remota.

Para VMs criadas usando o modelo de implantação clássico, você pode usar uma sessão de Azure PowerShell remota para a máquina virtual do Azure. Primeiro, você precisa instalar um certificado para o serviço de nuvem de hospedagem da máquina virtual. Acesse [Configurar acesso remoto do PowerShell seguro a máquinas virtuais do Azure](https://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) e baixe o arquivo de script **InstallWinRMCertAzureVM. ps1** para o computador local.

Em seguida, instale Azure PowerShell se ainda não tiver feito isso. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

Em seguida, abra um prompt de comando Azure PowerShell e altere a pasta atual para o local do arquivo de script **InstallWinRMCertAzureVM. ps1** . Para executar um script de Azure PowerShell, você deve definir a política de execução correta. Execute o comando **Get-ExecutionPolicy** para determinar o nível de política atual. Para obter informações sobre como definir o nível apropriado, consulte [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Em seguida, preencha o nome da sua assinatura do Azure, o nome do serviço de nuvem e o nome da máquina virtual (removendo os caracteres < e >) e execute estes comandos.

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

Você pode obter o nome de assinatura correto da propriedade *subscriptionname* da exibição do comando **Get-AzureSubscription** . Você pode obter o nome do serviço de nuvem para a máquina virtual na coluna *ServiceName* na exibição do comando **Get-AzureVM** .

Verifique se você tem o novo certificado. Abra um snap-in de certificados para o usuário atual e procure na pasta **Authorities\Certificates de certificação raiz confiável** . Você deve ver um certificado com o nome DNS do seu serviço de nuvem na coluna emitido para (exemplo: cloudservice4testing.cloudapp.net).

Em seguida, inicie uma sessão de Azure PowerShell remota usando esses comandos.

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

Depois de inserir credenciais de administrador válidas, você verá algo semelhante ao seguinte Azure PowerShell prompt:

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

A primeira parte desse prompt é o nome do serviço de nuvem que contém a VM de destino, que pode ser diferente de "cloudservice4testing.cloudapp.net". Agora você pode emitir Azure PowerShell comandos para esse serviço de nuvem para investigar os problemas mencionados e corrigir a configuração.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Para corrigir manualmente o Serviços de Área de Trabalho Remota porta TCP de escuta
No prompt de sessão de Azure PowerShell remota, execute este comando.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

A propriedade PortNumber mostra o número da porta atual. Se necessário, altere o número da porta Área de Trabalho Remota de volta para seu valor padrão (3389) usando este comando.

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389
```

Verifique se a porta foi alterada para 3389 usando este comando.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Saia da sessão de Azure PowerShell remota usando este comando.

```powershell
Exit-PSSession
```

Verifique se o ponto de extremidade Área de Trabalho Remota para a VM do Azure também está usando a porta TCP 3398 como sua porta interna. Reinicie a VM do Azure e tente a conexão Área de Trabalho Remota novamente.

## <a name="additional-resources"></a>Recursos adicionais
[Como redefinir uma senha ou o serviço de Área de Trabalho Remota para máquinas virtuais do Windows](../windows/reset-rdp.md)

[Como instalar e configurar o Azure PowerShell](/powershell/azure/overview)

[Solucionar problemas de conexões Secure Shell (SSH) para uma máquina virtual do Azure baseada em Linux](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Resolver problemas de acesso a uma aplicação em execução numa máquina virtual do Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

