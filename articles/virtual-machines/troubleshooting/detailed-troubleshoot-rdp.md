---
title: Resolução detalhada de problemas no ambiente de trabalho remoto em Azure Microsoft Docs
description: Reveja os passos detalhados de resolução de problemas para erros remotos no ambiente de trabalho onde não pode chegar a uma máquina virtual do Windows em Azure
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: não é possível ligar-se a ambientes de trabalho remotos remotos, desktop remoto, ambiente de trabalho remoto não pode ligar, erros remotos de ambiente de trabalho, resolução remota de problemas no ambiente de trabalho, problemas remotos no ambiente de trabalho
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: ea448b87f9e6954abecead2934bfb7f4ed04a9c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920149"
---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Passos de resolução de problemas detalhados para problemas de ligação de ambiente de trabalho remoto para VMs do Windows no Azure
Este artigo fornece passos detalhados de resolução de problemas para diagnosticar e corrigir complexos erros de ambiente de trabalho remoto para máquinas virtuais Azure baseadas no Windows.

> [!IMPORTANT]
> Para eliminar os erros mais comuns do Ambiente de Trabalho Remoto, certifique-se de ler o artigo básico de resolução de [problemas para o Remote Desktop](troubleshoot-rdp-connection.md) antes de prosseguir.

Pode encontrar uma mensagem de erro no Ambiente remoto que não se assemelhe a nenhuma das mensagens de erro específicas abrangidas pelo guia básico de resolução de [problemas](troubleshoot-rdp-connection.md)do Ambiente remoto . Siga estes passos para determinar por que razão o cliente Remote Desktop (RDP) não consegue ligar-se ao serviço RDP no Azure VM.


Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, também pode arquivar um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em Obter **Suporte**. Para obter informações sobre a utilização do Suporte Azure, leia as FAQ de suporte do [Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="components-of-a-remote-desktop-connection"></a>Componentes de uma ligação remota de ambiente de trabalho
Os seguintes componentes estão envolvidos numa ligação PDR:

![](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

Antes de prosseguir, pode ajudar a rever mentalmente o que mudou desde a última ligação bem sucedida do Ambiente de Trabalho Remoto ao VM. Por exemplo:

* O endereço IP público do VM ou o serviço de nuvem que contém o VM (também chamado de endereço IP virtual [VIP)](https://en.wikipedia.org/wiki/Virtual_IP_address)mudou. A falha do RDP pode ser porque a sua cache de cliente DNS ainda tem o *antigo endereço IP* registado para o nome DNS. Lave a cache do seu cliente DNS e tente ligar novamente o VM. Ou tente ligar-se diretamente com o novo VIP.
* Está a utilizar uma aplicação de terceiros para gerir as suas ligações Remote Desktop em vez de utilizar a ligação gerada pelo portal Azure. Verifique se a configuração da aplicação inclui a porta TCP correta para o tráfego de Ambiente de Trabalho Remoto. Pode verificar se esta porta tem uma máquina virtual clássica no [portal Azure,](https://portal.azure.com)clicando nas Definições do VM > Pontos Finais.

## <a name="preliminary-steps"></a>Etapas preliminares
Antes de prosseguir com a resolução detalhada de problemas,

* Verifique o estado da máquina virtual no portal Azure para obter quaisquer problemas óbvios.
* Siga os [passos de correção rápidos para erros de RDP comuns no guia básico](troubleshoot-rdp-connection.md#quick-troubleshooting-steps)de resolução de problemas .
* Para imagens personalizadas, certifique-se de que o seu VHD está devidamente preparado antes de o carregar. Para mais informações, consulte [Prepare um Windows VHD ou VHDX para fazer o upload para O Azure](../windows/prepare-for-upload-vhd-image.md).


Tente voltar a ligar-se ao VM via Remote Desktop após estes passos.

## <a name="detailed-troubleshooting-steps"></a>Passos detalhados de resolução de problemas
O cliente Remote Desktop pode não conseguir contactar o serviço Remote Desktop no Azure VM devido a problemas nas seguintes fontes:

* [Computador cliente de desktop remoto](#source-1-remote-desktop-client-computer)
* [Dispositivo de borda intranet da organização](#source-2-organization-intranet-edge-device)
* [Cloud service endpoint e lista de controlo de acesso (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Grupos de segurança da rede](#source-4-network-security-groups)
* [VM Azure baseado no Windows](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>Fonte 1: Computador cliente do ambiente de trabalho remoto
Verifique se o seu computador pode fazer ligações de Ambiente de Trabalho Remoto a outro computador no local, baseado no Windows.

![](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

Se não puder, verifique as seguintes definições no seu computador:

* Uma definição de firewall local que está bloqueando o tráfego remoto do ambiente de trabalho.
* Software de procuração de cliente instalado localmente que está a impedir ligações remotas de ambiente de trabalho.
* Software de monitorização de rede instalado localmente que está a impedir ligações remotas de ambiente de trabalho.
* Outros tipos de software de segurança que monitorizam o tráfego ou permitem/desautorizar tipos de tráfego específicos que estão a impedir as ligações do Ambiente de Trabalho Remoto.

Em todos estes casos, desative temporariamente o software e tente ligar-se a um computador no local através do Remote Desktop. Se conseguir descobrir a causa real desta forma, trabalhe com o seu administrador de rede para corrigir as definições de software para permitir ligações ao Ambiente de Trabalho Remoto.

## <a name="source-2-organization-intranet-edge-device"></a>Fonte 2: Dispositivo de borda intranet da organização
Verifique se um computador ligado diretamente à Internet pode fazer ligações remotas de ambiente de trabalho à sua máquina virtual Azure.

![](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

Se não tiver um computador que esteja diretamente ligado à Internet, crie e teste com uma nova máquina virtual Azure num grupo de recursos ou serviço na nuvem. Para mais informações, consulte [Criar uma máquina virtual com windows em Azure](../virtual-machines-windows-hero-tutorial.md). Pode eliminar a máquina virtual e o grupo de recursos ou o serviço de nuvem, após o teste.

Se conseguir criar uma ligação Remote Desktop com um computador diretamente ligado à Internet, verifique o dispositivo intranet edge da organização para:

* Uma firewall interna bloqueando ligações HTTPS à Internet.
* Um servidor proxy que impede as ligações remotas de ambiente de trabalho.
* Software de deteção de intrusões ou monitorização de rede em dispositivos na sua rede de bordas que está a impedir ligações remotas de ambiente de trabalho.

Trabalhe com o seu administrador de rede para corrigir as definições do dispositivo intranet edge da sua organização para permitir ligações de ambiente de trabalho remoto baseados em HTTPS à Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Fonte 3: Ponto final do serviço em nuvem e ACL

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Para VMs criados usando o modelo de implementação Classic, verifique se outro VM Azure que se encontra no mesmo serviço de nuvem ou rede virtual pode fazer ligações remotas de desktop ao seu VM Azure.

![](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> Para máquinas virtuais criadas em Gestor de Recursos, salte para [fonte 4: Grupos](#source-4-network-security-groups)de segurança de rede .

Se não tiver outra máquina virtual no mesmo serviço de nuvem ou rede virtual, crie uma. Siga os passos em [Criar uma máquina virtual que executa o Windows em Azure](../virtual-machines-windows-hero-tutorial.md). Elimine a máquina virtual de teste após a conclusão do teste.

Se conseguir ligar via Remote Desktop a uma máquina virtual no mesmo serviço de nuvem ou rede virtual, verifique estas definições:

* A configuração do ponto final para o tráfego de ambiente de trabalho remoto no VM alvo: A porta TCP privada do ponto final deve coincidir com a porta TCP em que o serviço de ambiente de trabalho remoto do VM está a ouvir (o predefinido é 3389).
* O ACL para o ponto final de tráfego do Ambiente remoto no VM alvo: Os ACLs permitem especificar ou negar o tráfego de entrada da Internet com base no seu endereço IP de origem. Os ACLs mal configurados podem impedir a entrada do tráfego remoto do ambiente de trabalho até ao ponto final. Verifique os seus ACLs para garantir que o tráfego de entrada a partir dos seus endereços IP públicos do seu proxy ou outro servidor de borda é permitido. Para mais informações, consulte O que é uma Lista de Controlo de Acesso à [Rede (ACL)?](../../virtual-network/virtual-networks-acl.md)

Para verificar se o ponto final é a origem do problema, retire o ponto final atual e crie um novo, escolhendo uma porta aleatória no intervalo 49152-65535 para o número de porta externa. Para mais informações, consulte [Como configurar pontos finais para uma máquina virtual](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="source-4-network-security-groups"></a>Fonte 4: Grupos de Segurança da Rede
Os Grupos de Segurança da Rede permitem um maior controlo granular do tráfego de entrada e saída permitido. Pode criar regras abrangendo subredes e serviços de nuvem numa rede virtual Azure.

Utilize a [verificação do fluxo de IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) para confirmar se uma regra num Grupo de Segurança de Rede está a bloquear o tráfego de ou para uma máquina virtual. Também pode rever regras eficazes do grupo de segurança para garantir a existência da regra NSG de entrada e é priorizada para a porta RDP (padrão 3389). Para mais informações, consulte a utilização de regras de [segurança eficazes para resolver o fluxo](../../virtual-network/diagnose-network-traffic-filter-problem.md)de tráfego vm .

## <a name="source-5-windows-based-azure-vm"></a>Fonte 5: Azure VM baseado no Windows
![](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

Siga as instruções [deste artigo](../windows/reset-rdp.md). Este artigo repõe o serviço remote Desktop na máquina virtual:

* Ativar a regra de predefinição do Windows Firewall "Remote Desktop" (porta TCP 3389).
* Ativar as ligações de ambiente de trabalho remoto, definindo o HKLM\System\CurrentControlSet\Control\Control\Terminal Server\fDenyTSConnections valor de registo para 0.

Tente a ligação do seu computador de novo. Se ainda não conseguir ligar-se via Remote Desktop, verifique se há problemas que se aseguir:

* O serviço Remote Desktop não está a funcionar no VM alvo.
* O serviço Remote Desktop não está a ouvir na porta TCP 3389.
* O Windows Firewall ou outra firewall local tem uma regra de saída que está a impedir o tráfego de Ambiente de Trabalho Remoto.
* O software de deteção de intrusões ou monitorização de rede em funcionamento na máquina virtual Azure está a impedir as ligações remotas de ambiente de trabalho.

Para VMs criados usando o modelo de implementação clássico, você pode usar uma sessão remota Azure PowerShell para a máquina virtual Azure. Primeiro, é necessário instalar um certificado para o serviço de nuvem de hospedagem da máquina virtual. Aceda ao [Configure Secure Remote PowerShell Access to Azure Virtual Machines](https://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) e descarregue o ficheiro de script **InstallWinRMCertAzureVM.ps1** para o seu computador local.

Em seguida, instale o Azure PowerShell se ainda não o fez. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

Em seguida, abra um pedido de comando Azure PowerShell e altere a pasta atual para a localização do ficheiro de script **InstallWinRMCertAzureVM.ps1.** Para executar um script Azure PowerShell, tem de definir a política de execução correta. Execute o comando **Get-ExecutionPolicy** para determinar o seu nível de política atual. Para obter informações sobre a definição do nível adequado, consulte [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Em seguida, preencha o nome de subscrição Do Azure, o nome do serviço na nuvem e o nome da máquina virtual (removendo os caracteres < e >), e, em seguida, execute estes comandos.

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

Pode obter o nome de subscrição correto a partir da propriedade *SubscriçãoName* do ecrã do comando **Get-AzureSubscription.** Pode obter o nome de serviço na nuvem da máquina virtual a partir da coluna *ServiceName* no visor do comando **Get-AzureVM.**

Verifique se tem o novo certificado. Abra um snap-in de Certificados para o utilizador atual e procure na pasta **Trusted Root Certification Authorities\Certificates.** Deve ver um certificado com o nome DNS do seu serviço na nuvem na coluna Emitida (exemplo: cloudservice4testing.cloudapp.net).

Em seguida, inicie uma sessão remota de PowerShell Azure utilizando estes comandos.

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

Depois de introduzir credenciais de administrador válidas, deve ver algo semelhante ao seguinte pedido da PowerShell Do IA:

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

A primeira parte desta solicitação é o seu nome de serviço na nuvem que contém o Alvo VM, que pode ser diferente de "cloudservice4testing.cloudapp.net". Agora pode emitir comandos Azure PowerShell para este serviço na nuvem para investigar os problemas mencionados e corrigir a configuração.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Para corrigir manualmente os Serviços de Ambiente de Trabalho Remoto souvindo a porta TCP
No aviso de sessão remota da PowerShell, execute este comando.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

A propriedade PortNumber mostra o número de porta atual. Se necessário, mude o número da porta remote Desktop de volta para o seu valor predefinido (3389) utilizando este comando.

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389
```

Verifique se a porta foi alterada para 3389 utilizando este comando.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Saia da sessão remota Azure PowerShell utilizando este comando.

```powershell
Exit-PSSession
```

Verifique se o ponto final do Ambiente de Trabalho Remoto para o Azure VM também está a utilizar a porta TCP 3398 como porta interna. Reinicie o VM Azure e tente novamente a ligação Remote Desktop.

## <a name="additional-resources"></a>Recursos adicionais
[Como redefinir uma palavra-passe ou o serviço de ambiente de trabalho remoto para máquinas virtuais windows](../windows/reset-rdp.md)

[Como instalar e configurar o Azure PowerShell](/powershell/azure/overview)

[Ligações De troubleshoot Secure Shell (SSH) a uma máquina virtual Azure baseada em Linux](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Resolver problemas de acesso a uma aplicação em execução numa máquina virtual do Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

