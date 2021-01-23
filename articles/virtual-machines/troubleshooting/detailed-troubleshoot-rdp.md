---
title: Resolução de problemas remotos detalhados no ambiente de trabalho em Azure | Microsoft Docs
description: Reveja etapas detalhadas de resolução de problemas para erros remotos no ambiente de trabalho onde não é possível a uma máquina virtual do Windows em Azure
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: não pode ligar-se a ambientes de trabalho remotos, ambientes de trabalho remotos, ambientes de trabalho remotos não podem ligar- se, erros remotos no ambiente de trabalho, resolução remota de problemas no ambiente de trabalho, problemas remotos no ambiente de trabalho
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: a17f3992663fe6de0751f7bc537297b9ba70cfc5
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98738140"
---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Passos de resolução de problemas detalhados para problemas de ligação de ambiente de trabalho remoto para VMs do Windows no Azure
Este artigo fornece medidas detalhadas de resolução de problemas para diagnosticar e corrigir erros complexos de ambiente de trabalho remoto para máquinas virtuais Azure baseadas no Windows.

> [!IMPORTANT]
> Para eliminar os erros mais comuns do Ambiente de Trabalho Remoto, certifique-se de ler [o artigo básico de resolução de problemas para o Ambiente de Trabalho Remoto](troubleshoot-rdp-connection.md) antes de prosseguir.

Pode encontrar uma mensagem de erro do Ambiente de Trabalho remoto que não se assemelhe a nenhuma das mensagens de erro específicas abrangidas [pelo guia básico de resolução de problemas do Ambiente de Trabalho Remoto](troubleshoot-rdp-connection.md). Siga estes passos para determinar por que razão o cliente remote desktop (RDP) não consegue ligar-se ao serviço RDP no Azure VM.


Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn azure e nos fóruns Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, também pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em Obter **Apoio.** Para obter informações sobre a utilização do Suporte Azure, leia o [Microsoft Azure Support FAQ](https://azure.microsoft.com/support/faq/).

## <a name="components-of-a-remote-desktop-connection"></a>Componentes de uma ligação de ambiente de trabalho remoto
Os seguintes componentes estão envolvidos numa ligação RDP:

![Um diagrama que mostra os componentes envolvidos numa ligação de ambiente de trabalho remoto (RDP).](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

Antes de prosseguir, pode ajudar a rever mentalmente o que mudou desde a última ligação de desktop remota bem sucedida ao VM. Por exemplo:

* O endereço IP público do VM ou do serviço de nuvem que contém o VM (também chamado de endereço IP virtual [VIP)](https://en.wikipedia.org/wiki/Virtual_IP_address)mudou. A falha do PDR pode ser porque a cache do seu cliente DNS ainda tem o *antigo endereço IP* registado para o nome DNS. Lave a cache do cliente DNS e tente ligar novamente o VM. Ou tente ligar-se diretamente com o novo VIP.
* Está a utilizar uma aplicação de terceiros para gerir as suas ligações remote desktop em vez de utilizar a ligação gerada pelo portal Azure. Verifique se a configuração da aplicação inclui a porta TCP correta para o tráfego de ambiente de trabalho remoto. Pode verificar esta porta para obter uma máquina virtual clássica no [portal Azure,](https://portal.azure.com)clicando nas Definições do VM > Endpoints.

## <a name="preliminary-steps"></a>Etapas preliminares
Antes de prosseguir com a resolução detalhada dos problemas,

* Verifique o estado da máquina virtual no portal Azure para quaisquer problemas óbvios.
* Siga os [passos de correção rápida para erros rdp comuns no guia básico de resolução de problemas](troubleshoot-rdp-connection.md#quick-troubleshooting-steps).
* Para imagens personalizadas, certifique-se de que o seu VHD está devidamente preparado antes de o fazer. Para obter mais informações, consulte [Prepare um VHD ou VHDX do Windows para fazer o upload para a Azure](../windows/prepare-for-upload-vhd-image.md).


Tente voltar a ligar-se ao VM via Remote Desktop após estes passos.

## <a name="detailed-troubleshooting-steps"></a>Passos detalhados de resolução de problemas
O cliente remote desktop pode não ser capaz de chegar ao serviço de desktop remoto no Azure VM devido a problemas nas seguintes fontes:

* [Computador cliente de desktop remoto](#source-1-remote-desktop-client-computer)
* [Dispositivo de borda intranet da organização](#source-2-organization-intranet-edge-device)
* [Lista de controlo de serviços em nuvem e acesso (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Grupos de segurança de rede](#source-4-network-security-groups)
* [Azure VM baseado em janelas](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>Fonte 1: Computador cliente de desktop remoto
Verifique se o seu computador pode esclarilar ligações remotas a outro computador no local, o computador baseado no Windows.

![Um diagrama dos componentes numa ligação de Ambiente de Trabalho Remoto (RDP) com o cliente RDP realçado e uma seta apontando para outro computador no local indicando uma ligação.](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

Se não puder, verifique as seguintes definições no seu computador:

* Uma definição de firewall local que está bloqueando o tráfego de ambiente de trabalho remoto.
* Software de procuração de clientes instalado localmente que está a impedir ligações de ambiente de trabalho remoto.
* Software de monitorização de rede instalado localmente que está a impedir ligações de ambiente de trabalho remoto.
* Outros tipos de software de segurança que monitorizam o tráfego ou permitem/não permitem tipos específicos de tráfego que impedem ligações de ambiente de trabalho remoto.

Em todos estes casos, desative temporariamente o software e tente ligar-se a um computador no local através do Remote Desktop. Se conseguir descobrir a causa real desta forma, trabalhe com o administrador de rede para corrigir as definições de software para permitir ligações de Ambiente de Trabalho Remoto.

## <a name="source-2-organization-intranet-edge-device"></a>Fonte 2: Dispositivo de borda intranet de organização
Verifique se um computador ligado diretamente à Internet pode fazer ligações de ambiente de trabalho remoto à sua máquina virtual Azure.

![Um diagrama dos componentes numa ligação remote desktop (RDP) com um cliente RDP que está ligado à internet realçada e uma seta apontando para uma máquina virtual Azure indicando uma ligação.](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

Se não tiver um computador que esteja diretamente ligado à Internet, crie e teste com uma nova máquina virtual Azure num grupo de recursos ou serviço de nuvem. Para obter mais informações, consulte [Criar uma máquina virtual a executar o Windows em Azure.](../windows/quick-create-portal.md) Pode eliminar a máquina virtual e o grupo de recursos ou o serviço de nuvem, após o teste.

Se conseguir criar uma ligação de ambiente de trabalho remoto com um computador diretamente ligado à Internet, verifique se o dispositivo de borda intranet da sua organização é possível:

* Uma firewall interna que bloqueia as ligações HTTPS à Internet.
* Um servidor proxy que impede ligações de ambiente de trabalho remoto.
* Software de deteção de intrusões ou monitorização de rede em dispositivos na sua rede de arestas que está a impedir ligações de ambiente de trabalho remoto.

Trabalhe com o seu administrador de rede para corrigir as definições do dispositivo de borda intranet da sua organização para permitir ligações de ambiente de trabalho remoto baseadas em HTTPS para a Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Fonte 3: Ponto final do serviço em nuvem e ACL

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Para os VMs criados utilizando o modelo de implementação Classic, verifique se outro VM Azure que está no mesmo serviço de nuvem ou rede virtual pode fazer ligações de Ambiente de Trabalho Remoto ao seu VM Azure.

![Um diagrama dos componentes numa ligação remote desktop (RDP) com um VM Azure realçado e uma seta apontando para outro VM Azure dentro do mesmo serviço de nuvem indicando uma ligação.](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> Para máquinas virtuais criadas no Gestor de Recursos, salte para [a Fonte 4: Grupos de Segurança da Rede](#source-4-network-security-groups).

Se não tiver outra máquina virtual no mesmo serviço de nuvem ou rede virtual, crie uma. Siga os passos na [Criar uma máquina virtual que executa o Windows em Azure](../windows/quick-create-portal.md). Elimine a máquina virtual de teste após a conclusão do teste.

Se conseguir ligar através do Remote Desktop a uma máquina virtual no mesmo serviço de nuvem ou rede virtual, verifique estas definições:

* A configuração do ponto final para o tráfego de ambiente de trabalho remoto no VM alvo: A porta TCP privada do ponto final deve coincidir com a porta TCP na qual o serviço de ambiente de trabalho remoto da VM está a ouvir (por defeito é 3389).
* O ACL para o ponto de terminação de tráfego de ambiente de trabalho remoto no VM alvo: OS ACLs permitem especificar tráfego permitido ou negado da Internet com base no seu endereço IP de origem. ACLs configurados de forma dissigurada podem impedir a entrada do tráfego remoto de ambiente de trabalho para o ponto final. Consulte os seus ACLs para garantir que o tráfego de entrada dos seus endereços IP públicos do seu proxy ou outro servidor de borda é permitido. Para obter mais informações, consulte [o que é uma Lista de Controlo de Acesso à Rede (ACL)?](/previous-versions/azure/virtual-network/virtual-networks-acl)

Para verificar se o ponto final é a origem do problema, retire o ponto final atual e crie um novo, escolhendo uma porta aleatória na gama 49152-65535 para o número de porta externa. Para obter mais informações, consulte [como configurar pontos finais para uma máquina virtual.](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints?toc=/azure/virtual-machines/windows/classic/toc.json)

## <a name="source-4-network-security-groups"></a>Fonte 4: Grupos de Segurança da Rede
Os Grupos de Segurança da Rede permitem um maior controlo granular do tráfego permitido de entrada e saída. Pode criar regras abrangendo sub-redes e serviços em nuvem numa rede virtual Azure.

Utilize [o fluxo IP para](../../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) confirmar se uma regra de um Grupo de Segurança de Rede está a bloquear o tráfego de ou para uma máquina virtual. Também pode rever as regras eficazes do grupo de segurança para garantir que a regra NSG de entrada existe e é prioritária para a porta RDP (padrão 3389). Para obter mais informações, consulte [a utilização de regras de segurança eficazes para resolver o fluxo de tráfego de VM.](../../virtual-network/diagnose-network-traffic-filter-problem.md)

## <a name="source-5-windows-based-azure-vm"></a>Fonte 5: Azure VM baseado no Windows
![Um diagrama dos componentes numa ligação remote desktop (RDP) com um VM Azure realçado dentro de um serviço de nuvem e uma mensagem de que poderia ser uma "possível fonte de problemas". Uma linha azul indica que as regras do Grupo de Segurança da Rede podem estar a bloquear o tráfego de ou para o VM Azure.](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

Siga as instruções [deste artigo.](./reset-rdp.md) Este artigo reinicia o serviço de ambiente de trabalho remoto na máquina virtual:

* Ativar a regra predefinição do Windows Firewall "Remote Desktop" (porta TCP 3389).
* Ativar as ligações de ambiente de trabalho remoto definindo o valor do registo HKLM\System\CurrentControlSet\Control\Terminal\terminal Server\fDenyTSConnections valuey a 0.

Tente a ligação do seu computador novamente. Se ainda não conseguir ligar através do Ambiente de Trabalho Remoto, verifique se existem os seguintes problemas possíveis:

* O serviço de ambiente de trabalho remoto não está a funcionar no VM alvo.
* O serviço de ambiente de trabalho remoto não está a ouvir na porta TCP 3389.
* O Windows Firewall ou outra firewall local tem uma regra de saída que está a impedir o tráfego remoto do ambiente de trabalho.
* O software de deteção de intrusões ou monitorização de rede em execução na máquina virtual Azure está a impedir ligações de ambiente de trabalho remoto.

Para VMs criados usando o modelo de implementação clássico, pode utilizar uma sessão remota do Azure PowerShell para a máquina virtual Azure. Primeiro, é necessário instalar um certificado para o serviço de nuvem de hospedagem da máquina virtual. Vá ao [Configure Secure Remote PowerShell Access to Azure Virtual Machines](https://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) e descarregue o ficheiro de script **InstallWinRMCertAzureVM.ps1** para o computador local.

Em seguida, instale a Azure PowerShell se ainda não o fez. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/).

Em seguida, abra um pedido de comando Azure PowerShell e altere a pasta atual para a localização do ficheiro de script **InstallWinRMCertAzureVM.ps1.** Para executar um script Azure PowerShell, tem de definir a política de execução correta. Executar o comando **get-execuçãopolicy** para determinar o seu nível de política atual. Para obter informações sobre a definição do nível adequado, consulte [A Política de Execução de Conjuntos](/powershell/module/microsoft.powershell.security/set-executionpolicy).

Em seguida, preencha o nome de subscrição do Azure, o nome do serviço de nuvem e o nome da sua máquina virtual (removendo os caracteres < e >) e, em seguida, execute estes comandos.

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

Pode obter o nome de subscrição correto a partir da propriedade *SubscriptionName* do display do comando **Get-AzureSubscription.** Pode obter o nome de serviço na nuvem da máquina virtual a partir da coluna *ServiceName* no visor do comando **Get-AzureVM.**

Verifique se tem o novo certificado. Abra um encaixe de certificados para o utilizador atual e procure na pasta **De Certificação de Raiz Fidedigna\Certificados.** Deverá ver um certificado com o nome DNS do seu serviço de nuvem na coluna Emitida Para (exemplo: cloudservice4testing.cloudapp.net).

Em seguida, inicie uma sessão remota do Azure PowerShell utilizando estes comandos.

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

Depois de introduzir credenciais de administrador válidas, deverá ver algo semelhante ao seguinte pedido Azure PowerShell:

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

A primeira parte deste pedido é o nome do serviço de nuvem que contém o VM alvo, que pode ser diferente de "cloudservice4testing.cloudapp.net". Pode agora emitir comandos Azure PowerShell para este serviço de nuvem para investigar os problemas mencionados e corrigir a configuração.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Para corrigir manualmente os Serviços de Ambiente de Trabalho Remoto que ouvem a porta TCP
Na sessão remota de Azure PowerShell, executar este comando.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

A propriedade PortNumber mostra o número atual da porta. Se necessário, altere o número da porta de ambiente de trabalho remoto de volta para o seu valor predefinido (3389) utilizando este comando.

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389
```

Verifique se a porta foi alterada para 3389 utilizando este comando.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Saia da sessão remota do Azure PowerShell utilizando este comando.

```powershell
Exit-PSSession
```

Verifique se o ponto de terminação do Ambiente de Trabalho Remoto para o Azure VM também está a utilizar a porta TCP 3398 como porta interna. Reinicie o Azure VM e tente novamente a ligação remote desktop.

## <a name="additional-resources"></a>Recursos adicionais
[Como redefinir uma palavra-passe ou o serviço de ambiente de trabalho remoto para máquinas virtuais do Windows](./reset-rdp.md)

[Como instalar e configurar o Azure PowerShell](/powershell/azure/)

[Resolução de problemas Ligações Secure Shell (SSH) a uma máquina virtual Azure baseada em Linux](./troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json)

[Resolver problemas de acesso a uma aplicação em execução numa máquina virtual do Azure](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json)
