---
title: Detalhadas ambiente de trabalho remoto, resolução de problemas no Azure | Documentos da Microsoft
description: Reveja os passos de resolução de problemas detalhados para erros de área de trabalho remotos onde não é possível para um máquinas de virtuais do Windows no Azure
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: Não é possível ligar ao ambiente de trabalho remoto, resolver problemas de ambiente de trabalho remoto, não é possível ligar o ambiente de trabalho remoto, erros de área de trabalho remotos, resolução de problemas área de trabalho remoto, problemas de ambiente de trabalho remotos
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 4b4d2e2099f0d49c7dd9a150ac659ffde62eaa21
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "64693092"
---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Passos de resolução de problemas detalhados para problemas de ligação de ambiente de trabalho remoto para VMs do Windows no Azure
Este artigo fornece passos de resolução de problemas detalhados para diagnosticar e corrigir erros complexos de ambiente de trabalho remoto para máquinas de virtuais do Azure baseado no Windows.

> [!IMPORTANT]
> Para eliminar os erros mais comuns do ambiente de trabalho remoto, certifique-se de ler [o artigo de resolução de problemas básico para ambiente de trabalho remoto](troubleshoot-rdp-connection.md) antes de continuar.

Pode encontrar um ambiente de trabalho remoto a mensagem de erro que não se assemelham a qualquer uma das mensagens de erro específicas abordada [o guia de resolução de problemas do ambiente de trabalho remoto básico](troubleshoot-rdp-connection.md). Siga estes passos para determinar por que o cliente de ambiente de trabalho remoto (RDP) não consegue ligar ao serviço RDP na VM do Azure.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure no [do Azure do MSDN e os fóruns de Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, também pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **obter suporte**. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ de suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="components-of-a-remote-desktop-connection"></a>Componentes de uma ligação de ambiente de trabalho remoto
Os seguintes componentes estão envolvidos numa ligação RDP:

![](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

Antes de continuar, poderá ser útil rever mentalmente, o que mudou desde a última ligação de ambiente de trabalho remoto com êxito à VM. Por exemplo:

* O endereço IP público da VM ou serviço cloud que contém a VM (também chamado do endereço IP virtual [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)) foi alterada. A falha RDP pode ser porque a cache de cliente DNS ainda tem o *endereço IP antigo* registado para o nome DNS. Libere o cache de cliente DNS e tente novamente ligar-se a VM. Ou tente ligar-se diretamente com o novo VIP.
* Está a utilizar uma aplicação de terceiros para gerir as ligações de ambiente de trabalho remoto em vez de utilizar a ligação gerada pelo portal do Azure. Certifique-se de que a configuração da aplicação inclui a porta TCP correta para o tráfego de ambiente de trabalho remoto. Pode verificar esta porta para uma máquina virtual clássica no [portal do Azure](https://portal.azure.com), ao clicar em definições da VM > pontos finais.

## <a name="preliminary-steps"></a>Etapas preliminares
Antes de avançar para a resolução de problemas detalhada,

* Verificar o estado da máquina virtual no portal do Azure para quaisquer problemas óbvios.
* Siga os [passos de correção rápida para erros RDP comuns no guia de resolução de problemas básico](troubleshoot-rdp-connection.md#quick-troubleshooting-steps).
* Para imagens personalizadas, certifique-se de que o VHD está devidamente preparado antes de carregá-lo. Para obter mais informações, consulte [preparar um VHD do Windows ou o VHDX para carregar para o Azure](../windows/prepare-for-upload-vhd-image.md).


Tente voltar a ligar à VM através do ambiente de trabalho remoto após estes passos.

## <a name="detailed-troubleshooting-steps"></a>Passos detalhados de resolução de problemas
O cliente de ambiente de trabalho remoto pode não ser capaz de alcançar o serviço de ambiente de trabalho remoto na VM do Azure devido a problemas com as seguintes origens:

* [Computador cliente de ambiente de trabalho remoto](#source-1-remote-desktop-client-computer)
* [Dispositivo de limite de intranet de organização](#source-2-organization-intranet-edge-device)
* [Ponto final de serviço de nuvem e de acesso (ACL) de lista de controlo](#source-3-cloud-service-endpoint-and-acl)
* [Grupos de segurança de rede](#source-4-network-security-groups)
* [VM do Azure baseado no Windows](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>Origem 1: Computador cliente de ambiente de trabalho remoto
Certifique-se de que o seu computador pode efetuar ligações de ambiente de trabalho remoto para o outro local, computador baseado em Windows.

![](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

Se não for possível, verifique as seguintes definições no seu computador:

* Uma definição de local firewall que está a bloquear o tráfego de ambiente de trabalho remoto.
* Software de proxy de cliente que está a impedir ligações de ambiente de trabalho remoto instalado localmente.
* Localmente instalado software que está a impedir ligações de ambiente de trabalho remoto da monitorização de rede.
* Outros tipos de software de segurança que monitorizar o tráfego de ou para permitirem/não permitam tipos específicos de tráfego que está a impedir ligações de ambiente de trabalho remoto.

Nestes casos, temporariamente desative o software e tentando estabelecer ligação a um computador no local através do ambiente de trabalho remoto. Se pode descobrir a causa real dessa maneira, trabalhe com o seu administrador de rede para corrigir as definições de software para permitir ligações de ambiente de trabalho remoto.

## <a name="source-2-organization-intranet-edge-device"></a>Origem 2: Dispositivo de limite de intranet de organização
Certifique-se de que um computador diretamente ligado à Internet pode efetuar ligações de ambiente de trabalho remoto à máquina virtual do Azure.

![](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

Se não tiver um computador que está diretamente ligado à Internet, criar e testar com uma nova máquina virtual do Azure no serviço de grupo ou a nuvem de recursos. Para obter mais informações, consulte [criar uma máquina virtual com Windows no Azure](../virtual-machines-windows-hero-tutorial.md). É possível eliminar a máquina virtual e o grupo de recursos ou o serviço em nuvem, após o teste.

Se pode criar uma ligação de ambiente de trabalho remoto com um computador ligado diretamente à Internet, verifique o seu dispositivo de limite de intranet de organização para:

* Um firewall interno bloquear ligações HTTPS para a Internet.
* Um servidor de proxy impedir ligações de ambiente de trabalho remoto.
* Detecção de intrusão ou de software em execução em dispositivos na sua rede de borda que está a impedir ligações de ambiente de trabalho remoto de monitorização de rede.

Contactar o administrador de rede para corrigir as definições do seu dispositivo de periferia de intranet de organização para permitir ligações de ambiente de trabalho remota baseado em HTTPS para a Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Origem de 3: Ponto final de serviço de nuvem e de ACL
Para as VMs criadas com o modelo de implementação clássica, certifique-se de que outra VM do Azure que está no mesmo serviço cloud ou de rede virtual podem efetuar ligações de ambiente de trabalho remoto à sua VM do Azure.

![](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> Para máquinas de virtuais criadas no Resource Manager, avance para o [4 de origem: Grupos de segurança de rede](#source-4-network-security-groups).

Se não tiver outra máquina virtual no mesmo serviço cloud ou rede virtual, crie uma. Siga os passos em [criar uma máquina virtual com Windows no Azure](../virtual-machines-windows-hero-tutorial.md). Elimine máquina virtual de teste depois do teste é concluído.

Se pode ligar através do ambiente de trabalho remoto a uma máquina virtual no mesmo serviço cloud ou rede virtual, verifique estas definições:

* A configuração de ponto final para o tráfego de ambiente de trabalho remoto na VM de destino: A porta TCP privada do ponto final tem de coincidir com a porta TCP no qual o serviço de ambiente de trabalho remoto da VM está à escuta (a predefinição é 3389).
* A ACL para o ponto de final de tráfego de ambiente de trabalho remoto na VM de destino: ACLs permitem-lhe especificar permitido ou negado o tráfego de entrada a partir da Internet com base no respetivo endereço IP de origem. ACLs mal configuradas podem impedir que o tráfego de entrada ambiente de trabalho remoto para o ponto final. Verifique suas ACLs para se certificar de que o tráfego de entrada de seus endereços IP públicos do seu proxy ou outro servidor de borda é permitido. Para obter mais informações, consulte [o que é uma lista de controlo de acesso de rede (ACL)?](../../virtual-network/virtual-networks-acl.md)

Para verificar se o ponto final é a origem do problema, remova o ponto final atual e criar um novo, a escolha de uma porta aleatória entre 49152 – 65535 para o número de porta externa. Para obter mais informações, consulte [como configurar pontos finais para uma máquina virtual](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="source-4-network-security-groups"></a>Origem de 4: Grupos de Segurança de Rede
Grupos de segurança de rede permitem um controle mais granular do tráfego de entrada e saído permitido. Pode criar regras de expansão de sub-redes e serviços em nuvem na rede virtual do Azure.

Utilize a [verificação do fluxo de IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) para confirmar se uma regra num Grupo de Segurança de Rede está a bloquear o tráfego de ou para uma máquina virtual. Também pode consultar as regras do grupo de segurança efetivas para se certificar de NSG "Permitir entrada" regra existe e está priorizada para a porta RDP (predefinição 3389). Para obter mais informações, consulte [fluxo de tráfego através de regras de segurança efetivas para resolver problemas da VM](../../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="source-5-windows-based-azure-vm"></a>Origem 5: VM do Azure baseado no Windows
![](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

Siga as instruções em [este artigo](../windows/reset-rdp.md). Este artigo repõe o serviço de ambiente de trabalho remoto na máquina virtual:

* Ative a regra predefinida do Firewall do Windows de "Ambiente de trabalho remoto" (a porta TCP 3389).
* Ative as ligações de ambiente de trabalho remoto, definindo o valor de registo HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections como 0.

Tente novamente a ligação do seu computador. Se ainda não conseguir ligar através do ambiente de trabalho remoto, verifique os seguintes problemas possíveis:

* O serviço de ambiente de trabalho remoto não está em execução na VM de destino.
* O serviço de ambiente de trabalho remoto não está à escuta na porta TCP 3389.
* Firewall do Windows ou outro firewall local tem uma regra de saída que está a impedir o tráfego de ambiente de trabalho remoto.
* Detecção de intrusão ou de software em execução na máquina virtual do Azure de monitorização de rede está a impedir ligações de ambiente de trabalho remoto.

Para as VMs criadas com o modelo de implementação clássica, pode utilizar uma sessão remota do PowerShell do Azure para a máquina virtual do Azure. Em primeiro lugar, terá de instalar um certificado para o serviço de nuvem de alojamento da máquina virtual. Aceda a [configurar o acesso remoto seguro do PowerShell para máquinas de virtuais do Azure](https://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) e Baixe o **InstallWinRMCertAzureVM.ps1** ficheiro de script para o computador local.

Em seguida, instale o Azure PowerShell se ainda não o fez. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

Em seguida, abra uma linha de comandos do Azure PowerShell e altere a pasta atual para a localização do **InstallWinRMCertAzureVM.ps1** ficheiro de script. Para executar um script do PowerShell do Azure, tem de definir a política de execução correta. Executar o **Get-ExecutionPolicy** comando para determinar o nível de política atual. Para obter informações sobre como definir o nível adequado, consulte [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Em seguida, preencha o nome da sua subscrição do Azure, o nome do serviço de nuvem e o nome da máquina virtual (remover o < e > caracteres), e, em seguida, execute estes comandos.

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

Pode obter o nome da subscrição correto do *SubscriptionName* propriedade da tela do **Get-AzureSubscription** comando. Pode obter o nome do serviço cloud para a máquina virtual a partir da *ServiceName* coluna na exibição da **Get-AzureVM** comando.

Verifique se tem o novo certificado. Abrir um snap-in de certificados para o usuário atual e examinar os **fidedigno certificação de raiz Fidedigna\Certificados** pasta. Deverá ver um certificado com o nome DNS do seu serviço cloud na coluna emitido para (exemplo: cloudservice4testing.cloudapp.net).

Em seguida, inicie uma sessão remota do PowerShell do Azure, utilizando estes comandos.

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

Após introduzir as credenciais de administrador válido, deverá ver algo semelhante à linha de comandos do Azure PowerShell seguinte:

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

A primeira parte desse prompt é o nome do serviço cloud que contenha o destino VM, que pode ser diferente da "cloudservice4testing.cloudapp.net". Agora, pode emitir do Azure PowerShell comandos para este serviço em nuvem investigar os problemas mencionados e corrija a configuração.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Para corrigir manualmente os serviços de ambiente de trabalho remoto à escuta a porta TCP
No prompt de sessão remoto do Azure PowerShell, execute este comando.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

A propriedade PortNumber mostra o número da porta atual. Se for necessário, altere o ambiente de trabalho remoto da porta número back para o valor predefinido (. 3389) ao utilizar este comando.

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389
```

Certifique-se de que a porta foi alterada para 3389 utilizando este comando.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Saia da sessão de Azure PowerShell remota, utilizando este comando.

```powershell
Exit-PSSession
```

Certifique-se de que o ponto de final de ambiente de trabalho remoto para a VM do Azure também está a utilizar a porta TCP 3398 como sua porta interna. Reinicie a VM do Azure e tente novamente a ligação de ambiente de trabalho remoto.

## <a name="additional-resources"></a>Recursos adicionais
[Como repor uma palavra-passe ou o serviço de ambiente de trabalho remoto para máquinas de virtuais do Windows](../windows/reset-rdp.md)

[Como instalar e configurar o Azure PowerShell](/powershell/azure/overview)

[Resolver problemas de ligações Secure Shell (SSH) para uma máquina virtual do Azure baseado em Linux](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Resolver problemas de acesso a uma aplicação em execução numa máquina virtual do Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

