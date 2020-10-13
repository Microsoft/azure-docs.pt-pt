---
title: Problemas de resolução de janelas Azure Guest Agent
description: Resolução de problemas o Windows Azure Guest Agent não está a trabalhar problemas
services: virtual-machines-windows
ms.service: virtual-machines-windows
author: dkdiksha
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/15/2020
ms.author: genli
ms.openlocfilehash: 597ea6e7ff7dbcfcb8a99d4e4de3c1b82915ee07
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90561266"
---
# <a name="troubleshooting-windows-azure-guest-agent"></a>Problemas de resolução de janelas Azure Guest Agent

O Windows Azure Guest Agent é um agente de máquinas virtuais (VM). Permite que o VM comunique com o Controlador de Tecidos (o servidor físico subjacente no qual o VM está hospedado) no endereço IP 168.63.129.16. Este é um endereço IP público virtual que facilita a comunicação. Para mais informações, consulte [o endereço IP 168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16).

 O VM que é migrado para Azure a partir de instalações ou que é criado usando uma imagem personalizada não tem Windows Azure Guest Agent instalado. Nestes cenários, tem de instalar manualmente o agente VM. Para obter mais informações sobre como instalar o Agente VM, consulte [a visão geral do Agente de Máquinas Virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows).

Depois de o Windows Azure Guest Agent ter sido instalado com sucesso, pode ver os seguintes serviços listados em services.msc no VM:
 
- Serviço de Agente Convidado Windows Azure
- Serviço de Telemetria
- Serviço de agente RD

**Serviço de Agente Convidado Windows Azure**: Este serviço é o serviço responsável por toda a sessão em WAppAgent.log. Este serviço é responsável por configurar várias extensões e comunicações de Hóspede para Anfitrião. 

**Serviço de telemetria**: Este serviço é responsável pelo envio dos dados de telemetria do VM para o servidor backend.

**Serviço de Agente RD**: Este serviço é responsável pela instalação do Agente Convidado. O Transparent Installer é também um componente do Rd Agent que ajuda a atualizar outros componentes e serviços do Agente Convidado. RDAgent também é responsável pelo envio de batimentos cardíacos do Guest VM para o Agente Anfitrião no servidor físico.

> [!NOTE]
> A partir da versão 2.7.41491.971 do VM Guest Agent, o componente de Telemetria está incluído no serviço RDAgent, portanto, você pode não ver este serviço de Telemetria listado em VMs recém-criados.

## <a name="checking-agent-status-and-version"></a>Verificação do estado e versão do agente

Aceda à página de propriedades VM no portal Azure e verifique o estado do **Agente**. Se o Windows Azure Guest Agent estiver a funcionar corretamente, o estado mostra **Ready**. Se o Agente VM estiver em **estado de Não Pronto,** as extensões e o **comando run** no portal Azure não funcionarão.

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>Agente VM de resolução de problemas que está em estado de não pronto

### <a name="step-1-check-whether-the-windows-azure-guest-agent-service-is-installed"></a>Passo 1 Verifique se o serviço de Agente Convidado do Windows Azure está instalado

- Verifique o pacote

    Localizar a pasta C:\WindowsAzure. Se vir a pasta GuestAgent que exibe o número da versão, isso significa que o Windows Azure Guest Agent foi instalado no VM. Também pode procurar o pacote instalado.  Se o Windows Azure Guest Agent for instalado no VM, a embalagem será guardada no seguinte local: `C:\windows\OEM\GuestAgent\VMAgentPackage.zip` .
    
    Pode executar o seguinte comando PowerShell para verificar se o Agente VM foi implantado no VM:
    
    `Get-AzVM -ResourceGroupName "RGNAME" -Name "VMNAME" -DisplayHint expand`
    
    Na saída, localize a propriedade **ProvisionVMAgent** e verifique se o valor está definido para **True**. Se for, isto significa que o agente está instalado no VM.
    
- Consulte os Serviços e Processos

   Vá à consola de Serviços (services.msc) e verifique o estado dos seguintes serviços: Windows Azure Guest Agent Service, serviço RDAgent, Windows Azure Telemetry Service e Serviço de Agente de Rede Windows Azure.
 
    Também pode verificar se estes serviços estão a funcionar examinando o Gestor de Tarefas para os seguintes processos:
       
    - WindowsAzureGuestAgent.exe: Serviço de Agente Convidado Windows Azure
    - WaAppAgent.exe: serviço RDAgent
    - WindowsAzureNetAgent.exe: Serviço de Agente de Rede Windows Azure
    - WindowsAzureTelemetryService.exe: Serviço de Telemetria Windows Azure

   Se não conseguir encontrar estes processos e serviços, isto indica que não tem o Windows Azure Guest Agent instalado.

- Verifique o Programa e Funcionalidade

    No Painel de Controlo, aceda a **Programas e Funcionalidades** para determinar se o serviço de Agente Convidado Do Windows Azure está instalado.

Se não encontrar pacotes, serviços e processos em funcionamento e nem sequer ver o Windows Azure Guest Agent instalado em Programas e Funcionalidades, tente [instalar o serviço de Agente Convidado do Windows Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows). Se o Agente Convidado não instalar corretamente, pode [instalar o agente VM offline](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline).

Se conseguir ver os serviços e eles estiverem a funcionar, reinicie o serviço que veja se o problema está resolvido. Se os serviços estiverem parados, inicie-os e espere alguns minutos. Em seguida, verifique se o **estado do Agente** está a reportar como **Ready**. Se descobrir que estes serviços estão em colapso, alguns processos de terceiros podem estar a causar a quebra destes serviços. Para uma maior resolução de problemas destes problemas, contacte [o Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="step-2-check-whether-auto-update-is-working"></a>Passo 2 Verifique se a atualização automática está a funcionar

O Windows Azure Guest Agent tem uma funcionalidade de atualização automática. Verificará automaticamente novas atualizações e instala-as-á automaticamente. Se a função de atualização automática não funcionar corretamente, tente desinstalar e instalar o Windows Azure Guest Agent utilizando os seguintes passos:

1. Se o Windows Azure Guest Agent aparecer em **Programas e Funcionalidades, desinstale**o Windows Azure Guest Agent.

2. Abra uma janela de pedido de comando que tenha privilégios de administrador.

3. Parem os serviços de Agente Convidado. Se os serviços não pararem, tem de definir os serviços para **arranque manual** e, em seguida, reiniciar o VM.
    ```
    net stop rdagent
    net stop WindowsAzureGuestAgent
    net stop WindowsAzureTelemetryService
    ```
1. Eliminar os Serviços de Agente Convidado:
    ```
    sc delete rdagent
    sc delete WindowsAzureGuestAgent
    sc delete WindowsAzureTelemetryService
    ```
1. Sob `C:\WindowsAzure` criar uma pasta que se chama OLD.

1. Mova quaisquer pastas que sejam chamadas Pacotes ou GuestAgent para a pasta OLD.

1. Descarregue e instale a versão mais recente do pacote de instalação do agente a partir [daqui.](https://go.microsoft.com/fwlink/?linkid=394789&clcid=0x409) Tem de ter direitos de administrador para completar a instalação.

1. Instale o Agente Convidado utilizando o seguinte comando:

    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log
    ```
    Em seguida, verifique se os Serviços de Agente Convidado começam corretamente.
 
    Em casos raros em que o Agente Convidado não se instala corretamente, pode [instalar o agente VM offline](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline).
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>Passo 3 Verifique se o VM pode ligar-se ao controlador de tecido

Utilize uma ferramenta como o PsPing para testar se o VM pode ligar-se a 168.63.129.16 nas portas 80, 32526 e 443. Se o VM não se ligar como esperado, verifique se a comunicação de saída sobre as portas 80, 443 e 32526 está aberta na firewall local no VM. Se este endereço IP estiver bloqueado, o Agente VM pode apresentar comportamentos inesperados numa variedade de cenários.

## <a name="advanced-troubleshooting"></a>Resolução de problemas avançados

Os eventos para resolução de problemas do Windows Azure Guest Agent são registados nos seguintes ficheiros de registo:

- C:\WindowsAzure\Logs\WaAppAgent.log
- C:\WindowsAzure\Logs\TransparentInstaller.log

Seguem-se alguns cenários comuns em que o Windows Azure Guest Agent pode entrar **sem estado pronto** ou deixar de funcionar como esperado.

### <a name="agent-stuck-on-starting"></a>Agente preso em "Starting"

No registo WaAppAgent, pode ver que o agente está preso no processo iniciar e não pode iniciar.

**Informação de registo**
 
[00000007] [05/28/2019 12:58:50.90] [INFO] WindowsAzureGuestAgent a partir de 12:58:50.90] [INFO] WindowsAzureGuestAgent a partir. Versão 2.7.41491.901

**Análise**
 
O VM ainda está a executar a versão mais antiga do Windows Azure Guest Agent. Na pasta C:\WindowsAzure, pode notar que estão instaladas várias instâncias do Windows Azure Guest Agent, incluindo várias das mesmas versões. Como estão instaladas múltiplas instâncias de agente, o VM não inicia a versão mais recente do Windows Azure Guest Agent.

**Solução**

Desinstalar manualmente o Windows Azure Guest Agent e, em seguida, reinstalá-lo seguindo estes passos:

1. O Painel de Controlo Aberto > programas e funcionalidades e desinstale o Windows Azure Guest Agent.
1. Open Task Manager e pare os seguintes serviços: Windows Azure Guest Agent Service, serviço RDAgent, Windows Azure Telemetry Service e Serviço de Agente de Rede Windows Azure.
1. Em C:\WindowsAzure, crie uma pasta chamada OLD.
1. Mova quaisquer pastas que sejam chamadas Pacotes ou GuestAgent para a pasta OLD. Além disso, mova qualquer uma das pastas GuestAgent em C:\WindowsAzure\logs que começam como GuestAgent_x.x.xxxxxx para a pasta OLD.
1. Descarregue e instale a versão mais recente do agente MSI. Tem de ter direitos de administrador para completar a instalação.
1. Instale o Agente Convidado utilizando o seguinte comando MSI:
    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log`
    ```
1. Verifique se os serviços RDAgent, Windows Azure Guest Agent e Windows Azure Telemetria estão agora em funcionamento.
 
1. Verifique o waAppAgent.log para se certificar de que a versão mais recente do Windows Azure Guest Agent está em execução.
 
1. Elimine a pasta OLD em C:\WindowsAzure.
  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Não é possível ligar ao Ip do WireServer (HOST IP) 

Nota as seguintes entradas de erro em WaAppAgent.log e Telemetria.log:

**Informação de registo**

```Log sample
[ERROR] GetVersions() failed with exception: Microsoft.ServiceModel.Web.WebProtocolException: Server Error: Service Unavailable (ServiceUnavailable) ---> 
System.Net.WebException: The remote server returned an error: (503) Server Unavailable.
```
```Log sample
[00000011] [12/11/2018 06:27:55.66] [WARN]  (Ignoring) Exception while fetching supported versions from HostGAPlugin: System.Net.WebException: Unable to connect to the remote server 
---> System.Net.Sockets.SocketException: An attempt was made to access a socket in a way forbidden by its access permissions 168.63.129.16:32526
at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
at System.Net.ServicePoint.ConnectSocketInternal(Boolean connectFailure, Socket s4, Socket s6, Socket& socket, IPAddress& address, ConnectSocketState status, IAsyncResult asyncResult, Exception& exception)
--- End of inner exception stack trace ---
at System.Net.WebClient.DownloadDataInternal(Uri address, WebRequest& request)
at System.Net.WebClient.DownloadString(Uri address)
at Microsoft.GuestAgentHostPlugin.Client.GuestInformationServiceClient.GetVersions()
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.HostGAPluginUtility.UpdateHostGAPluginAvailability()`
```
**Análise**

O VM não pode chegar ao servidor anfitrião do wireserver.

**Solução**

1. Como o wireserver não é alcançável, ligue-se ao VM utilizando o Remote Desktop e tente aceder ao seguinte URL a partir de um navegador de Internet: http://168.63.129.16/?comp=versions 
1. Se não conseguir chegar ao URL a partir do passo 1, verifique a interface de rede para determinar se está definida como ativada por DHCP e tem DNS. Para verificar o estado do DHCP, da interface de rede, execute o seguinte comando:  `netsh interface ip show config` .
1. Se o DHCP estiver desativado, execute o seguinte certificando-se de que altera o valor em amarelo para o nome da sua interface: `netsh interface ip set address name="Name of the interface" source=dhcp` .
1. Verifique se existem problemas que possam ser causados por uma firewall, um representante ou outra fonte que possa estar a bloquear o acesso ao endereço IP 168.63.129.16.
1. Verifique se o Windows Firewall ou uma firewall de terceiros está a bloquear o acesso às portas 80, 443 e 32526. Para obter mais informações sobre o motivo pelo qual este endereço não deve ser bloqueado, consulte [o endereço IP 168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16).

### <a name="guest-agent-is-stuck-stopping"></a>Agente convidado está preso "Parar"  

Nota as seguintes entradas de erro em WaAppAgent.log:

**Informação de registo** 

```Log sample
[00000007] [07/18/2019 14:46:28.87] [WARN] WindowsAzureGuestAgent stopping.
[00000007] [07/18/2019 14:46:28.89] [INFO] Uninitializing StateExecutor with WaitForTerminalStateReachedOnEnd : True
[00000004] [07/18/2019 14:46:28.89] [WARN] WindowsAzureGuestAgent could not be stopped. Exception: System.NullReferenceException: Object reference not set to an instance of an object.
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorBase.WaitForExtensionWorkflowComplete(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorBase.Uninitialize(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorForCloud.Uninitialize(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentCore.Stop(Boolean waitForTerminalState)
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentService.DoStopService()
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentService.<>c__DisplayClass2.<OnStopProcessing>b__1()
```
**Análise**

O Windows Azure Guest Agent está preso no processo de paragem.

**Solução**

1. Certifique-se de que WaAppAgent.exe está a funcionar no VM. Se não estiver a funcionar, reinicie o serviço rdgagent e aguarde cinco minutos. Quando WaAppAgent.exe estiver a funcionar, acabe com o processo de WindowsAzureGuest.exe.
2. Se o passo 1 não resolver o problema, retire a versão atualmente instalada e instale manualmente a versão mais recente do agente.

### <a name="npcap-loopback-adapter"></a>Adaptador de loopback Npcap 

Nota as seguintes entradas de erro em WaAppAgent.log:
 
```Log sample
[00000006] [06/20/2019 07:44:28.93] [INFO]  Attempting to discover fabric address on interface Npcap Loopback Adapter.
[00000024] [06/20/2019 07:44:28.93] [WARN]  Empty DHCP option data returned
[00000006] [06/20/2019 07:44:28.93] [ERROR] Did not discover fabric address on interface Npcap Loopback Adapter
```
**Análise**

O adaptador de loopback Npcap é instalado no VM por Wireshark. O Wireshark é uma ferramenta de código aberto para perfis de tráfego de rede e análise de pacotes. Tal ferramenta é frequentemente referida como um analisador de rede, analisador de protocolo de rede ou sniffer.

**Solução**

O adaptador Npcap Loopback é provavelmente instalado pelo WireShark. Tente desativá-lo e, em seguida, verifique se o problema está resolvido.

## <a name="next-steps"></a>Passos seguintes

Para resolver mais problemas, o problema do "Windows Azure Guest Agent não está a funcionar", [contacte o suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
