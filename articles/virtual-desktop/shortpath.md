---
title: Windows Virtual Desktop RDP Shortpath (pré-visualização)
titleSuffix: Azure
description: Como configurar o Shortpath RDP (pré-visualização) para o Windows Virtual Desktop.
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: ee37ab90910058378172223a3435047346f5fe7c
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701792"
---
# <a name="windows-virtual-desktop-rdp-shortpath-preview"></a>Windows Virtual Desktop RDP Shortpath (pré-visualização)

> [!IMPORTANT]
> RdP Shortpath está atualmente em pré-visualização pública.
> Esta pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos usá-lo para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

RDP Shortpath é uma funcionalidade do Windows Virtual Desktop que estabelece um transporte direto baseado em UDP entre o cliente de desktop remoto e o anfitrião de sessão. O RDP utiliza este transporte para fornecer Remote Desktop e RemoteApp, oferecendo uma melhor fiabilidade e latência consistente.

## <a name="key-benefits"></a>Principais vantagens

* O transporte de caminho curto RDP baseia-se no topo do protocolo de  [controlo de tarifas universais (URCP)](https://www.microsoft.com/en-us/research/publication/urcp-universal-rate-control-protocol-for-real-time-communication-applications/)altamente eficiente. A URCP melhora a UDP com monitorização ativa das condições da rede e proporciona uma utilização justa e completa do link. A URCP opera com baixos níveis de atraso e perda, conforme necessário pelo Remote Desktop. A URCP consegue o melhor desempenho através de parâmetros de rede de aprendizagem dinâmica e fornecendo protocolo com um mecanismo de controlo de taxas.
* O ViaDR estabelece a conectividade direta entre o cliente de desktop remoto e o anfitrião da sessão. A conectividade direta reduz a dependência dos gateways de desktop virtual do Windows, melhora a fiabilidade da ligação e aumenta a largura de banda disponível para cada sessão de utilizador.
* A remoção de relé adicional reduz o tempo de ida e volta, o que melhora a experiência do utilizador com aplicações sensíveis à latência e métodos de entrada.
* O Caminho Curto RDP traz apoio para [configurar prioridade de Qualidade de Serviço (QoS)](./rdp-quality-of-service-qos.md) para ligações RDP através de uma marca de Código de Serviços Diferenciados (DSCP)
* O transporte RDP Shortpath permite [limitar o tráfego de rede de saída](./rdp-bandwidth.md#limit-network-bandwidth-use-with-throttle-rate) especificando uma taxa de aceleração para cada sessão.

## <a name="connection-security"></a>Segurança da Ligação

O RDP Shortpath está a alargar as capacidades de transporte multi-transport RDP. Não substitui o transporte de ligação inversa, mas complementa-o. Toda a corretagem inicial da sessão é gerida através da infraestrutura virtual do Windows Desktop.

A porta UDP 3390 é utilizada apenas para o tráfego de shortpath que é autenticado sobre o transporte de ligação inversa. O ouvinte do RDP Shortpath ignora todas as tentativas de ligação ao ouvinte, a menos que correspondam à sessão de ligação inversa.

O RDP Shortpath utiliza uma ligação TLS entre o cliente e o anfitrião da sessão utilizando os certificados do anfitrião da sessão. Por predefinição, o certificado utilizado para a encriptação RDP é autogerido pelo SO durante a implementação. Se desejar, os clientes podem utilizar certificados geridos centralmente emitidos pela autoridade de certificação da empresa. Para obter mais informações sobre as configurações do certificado, consulte [a documentação do Windows Server](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations.md).

## <a name="rdp-shortpath-connection-sequence"></a>Sequência de ligação RDP Shortpath

Após a instalação do [transporte de ligação inversa,](./network-connectivity.md)o cliente e o anfitrião da sessão estabelecem a ligação RDP e negoceiam capacidades de vários transportes. Etapas adicionais descritas abaixo:

1. O anfitrião da sessão envia a lista dos seus endereços IPv4 e IPv6 privados e públicos ao cliente.
2. O cliente inicia o fio de fundo para estabelecer um transporte paralelo baseado na UDP diretamente para um dos endereços IP do anfitrião.
3. Enquanto o cliente está a sondar os endereços IP fornecidos, continua o estabelecimento de ligação inicial sobre o transporte de ligação inversa para garantir que não haja atrasos na ligação do utilizador.
4. Se o cliente tiver uma linha de visão direta e a configuração da firewall estiver correta, o cliente estabelece uma ligação TLS segura com o anfitrião da sessão.
5. Depois de estabelecer o transporte Shortpath, o RDP move todos os Canais Virtuais Dinâmicos (DVCs), incluindo gráficos remotos, entrada e reorientação do dispositivo para o novo transporte.
6. Se uma firewall ou topologia de rede impedir o cliente de estabelecer conectividade UDP direta, o RDP continua com um transporte de ligação inversa.

O diagrama abaixo dá uma visão geral de alto nível da ligação da rede RDP Shortpath.

:::image type="content" source="media/rdp-shortpath-connections.svg" alt-text="Diagrama de Ligações de Rede DE Shortpath RDP" lightbox="media/rdp-shortpath-connections.svg":::

## <a name="requirements"></a>Requisitos

Para suportar o Caminho Curto RDP, o cliente virtual do Windows desktop precisa de uma linha de visão direta para o anfitrião da sessão. Pode obter uma linha de visão direta utilizando uma das seguintes tecnologias:

* [ExpressRoute private peering](../expressroute/expressroute-circuit-peerings.md)
* [VPN site-to-site (baseado em IPsec)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [VPN ponto-a-local (baseado em IPsec)](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [Atribuição de endereços IP públicos](../virtual-network/virtual-network-public-ip-address.md)

Se estiver a utilizar outros tipos de VPN para se ligar à rede virtual Azure, recomendamos a utilização de VPN baseada em UDP para obter os melhores resultados. Embora a maioria das soluções VPN baseadas em TCP englobam todos os pacotes IP, incluindo a UDP, eles adicionam a sobrecarga herdada do controlo de congestionamento TCP que iria retardar o desempenho do RDP.

A linha de visão direta significa que as firewalls não estão a bloquear a porta UDP 3390 e o cliente pode ligar-se diretamente ao anfitrião da sessão.

## <a name="enabling-rdp-shortpath-preview"></a>Habilitar a pré-visualização do ViadP Shortpath

Para participar na pré-visualização do Via Final RDP, é necessário ativar o ouvinte do PDR no anfitrião da sessão. Pode ativar o Via Final RDP em qualquer número de anfitriões de sessão utilizados no seu ambiente. Não há necessidade de permitir o ViaDP Shortpath em todos os anfitriões da piscina.
Para ativar o ouvinte do Shortpath, é necessário configurar os seguintes valores de registo:

> [!WARNING]
> Podem ocorrer problemas graves se modificar o registo incorretamente utilizando o Editor de Registo ou utilizando outro método. Estes problemas podem exigir que reinstale o seu sistema operativo. A Microsoft não garante que estes problemas possam ser resolvidos. O utilizador modifica o registo por sua conta e risco.

1. No anfitrião da sessão, Inicie Regedit.exe e, em seguida, navegue para o seguinte local:

    ```cmd
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations
    ```

2. Crie um novo valor **DWORD** chamado **fUseUdpPortRedirector** e deite-o a **1** (decimal)
3. Crie um novo valor **DWORD** chamado **UdpPortNumber** e coloque-o em **3390** (decimal)
4. Demita-se editor de registos.
5. Anfitrião da sessão de reinício

Também pode executar os seguintes cmdlets numa janela elevada do PowerShell para definir estes valores de registo:

```powershell
$WinstationsKey = 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations'
New-ItemProperty -Path $WinstationsKey -Name 'fUseUdpPortRedirector' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 1 -Force
New-ItemProperty -Path $WinstationsKey -Name 'UdpPortNumber' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 3390 -Force
```

Também pode usar a política do Grupo PowerShell para configurar

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'fUseUdpPortRedirector' -Value 1 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'UdpPortNumber' -Value 3390 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
```

## <a name="configure-windows-defender-firewall-with-advanced-security"></a>Configurar firewall do Windows Defender com Segurança Avançada

Para permitir o tráfego de rede de entrada para o Via-Curto RDP, utilize o Firewall do Windows Defender com o nó de Segurança Avançada no snap-in MMC de Gestão de Políticas de Grupo para criar regras de firewall.

1. Abra a consola de gestão de políticas de grupo para [firewall do Windows Defender com Segurança Avançada](/windows/security/threat-protection/windows-firewall/open-the-group-policy-management-console-to-windows-firewall-with-advanced-security).
2. No painel de navegação, selecione **Regras de Entrada**.
3. Selecione **Ação** e, em seguida, selecione **Nova regra**.
4. Na página **'Tipo regra'** do novo assistente de entrada, selecione **'Personalizado'** e, em seguida, selecione **Seguinte**.
5. Na página **do Programa,** selecione **Este percurso de programa** e escreva "%SystemRoot%\system32\svchost.exe" e, em seguida, selecione **Seguinte**.
6. Na página **Protocolo e Portas,** selecione o tipo de protocolo UDP. Na **porta local,** selecione "Portas Específicas" e escreva em 3390.
7. Na página **Âmbito,** pode especificar que a regra se aplica apenas ao tráfego de rede para ou a partir dos endereços IP introduzidos nesta página. Configure conforme apropriado para o seu design e, em seguida, selecione **Next**.
8. Na página **Ação,** selecione **Deixe a ligação** e, em seguida, selecione **Seguinte**.
9. Na página **'Perfil',** selecione os tipos de localização da rede a que esta regra se aplica e, em seguida, selecione **Seguinte**.
10. Na página **Nome,** escreva um nome e descrição para a sua regra e, em seguida, **selecione Terminar**.

Pode verificar se a nova regra corresponde às imagens abaixo: :::image type="content" source="media/rdp-shortpath-firewall-general-tab.png" alt-text="Screenshot do separador Geral para configuração de Firewall para Ligações de Rede DE Shortpath RDP" lightbox="media/rdp-shortpath-firewall-general-tab.png":::

:::image type="content" source="media/rdp-shortpath-firewall-service-settings.png" alt-text="Screenshot do separador Programas e Serviços para configuração firewall para ligações de rede de shortpath RDP" lightbox="media/rdp-shortpath-firewall-service-settings.png":::

:::image type="content" source="media/rdp-shortpath-firewall-protocol-and-ports.png" alt-text="Screenshot do separador Protocolos e Portas para configuração de Firewall para Ligações de Rede DE Shortpath RDP" lightbox="media/rdp-shortpath-firewall-protocol-and-ports.png":::

Também pode utilizar o PowerShell para configurar o Windows Firewall:

```powershell
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP'  -PolicyStore PersistentStore -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True
```

### <a name="using-powershell-to-configure-windows-defender-firewall"></a>Utilizar o PowerShell para configurar o Firewall do Windows Defender

Também pode usar a política do Grupo PowerShell para configurar

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
$gpoSession = Open-NetGPO -PolicyStore "$domainName\$policyName"
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP'  -PolicyStore PersistentStore -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True
```

## <a name="configuring-azure-network-security-group"></a>Configurar o Grupo de Segurança da Rede Azure

Para permitir o acesso ao ouvinte rdp shortpath através dos limites de segurança da rede, é necessário configurar o Azure Network Security Group para permitir a entrada na porta UDP 3390.
Siga a [documentação](../virtual-machines/windows/nsg-quickstart-portal.md) do grupo de segurança da rede para criar uma regra de segurança de entrada que permite o tráfego com os seguintes parâmetros:

* **Fonte**  -  **Qualquer** ou a gama de IP onde os clientes estão a residir
* **Gamas portuárias de origem** - * *\** _ _ **Destino**  -  **Qualquer**
* **Gamas portuárias de**  -  destino **3390**
* **Protocolo**  -  **UDP**
* **Ação**  -  **Permitir**
* Alterar opcionalmente a **Prioridade**. A prioridade afeta a ordem em que as regras são aplicadas: quanto menor o valor numérico, mais cedo a regra é aplicada.
* **Nome** - - **RDP Shortpath**

### <a name="disabling-rdp-shortpath-for-a-specific-subnet"></a>Desativação do Caminho Curto RDP para uma sub-rede específica

Se precisar de bloquear sub-redes específicas da utilização do transporte RDP Shortpath, pode configurar grupos de segurança de rede adicionais especificando as gamas SOURCE IP.

## <a name="verifying-the-connectivity"></a>Verificação da conectividade

### <a name="using-connection-information-dialog"></a>Utilização do diálogo informação de ligação

Para verificar se as ligações estão a utilizar o Shortpath RDP, abra o diálogo "Informação de Ligação", clicando no ícone da antena na barra de ferramentas de ligação.

:::image type="content" source="media/rdp-shortpath-connection-bar.png" alt-text="Imagem da Barra de Conexão de Ambiente de Trabalho Remoto":::

:::image type="content" source="media/rdp-shortpath-connection-info.png" alt-text="Imagem do diálogo de Informação de Conexão de Ambiente de Trabalho Remoto":::

### <a name="using-event-logs"></a>Usando registos de eventos

Para verificar esta sessão está a utilizar o transporte RDP Shortpath:

1. Ligue-se ao ambiente de trabalho do VM utilizando o cliente de ambiente de trabalho virtual do Windows.
2. Lançar o Observador de Eventos e navegar para o seguinte nó: **Aplicações e Registos de Serviços > Microsoft > Windows > RemoteDesktopServices-RdpCoreCDV > Microsoft-Windows-RemoteDesktopServices-RdpCoreCDV/Operacional**
3. Para determinar se o transporte de PDR é utilizado, procure o ID 131 do evento.

### <a name="using-log-analytics-to-verify-shortpath-connectivity"></a>Utilização de Log Analytics para verificar a conectividade shortpath

Se estiver a utilizar [o Azure Log Analytics,](./diagnostics-log-analytics.md)pode monitorizar as ligações consultando a [tabela WVDConnections](/azure/azure-monitor/reference/tables/wvdconnections). Uma coluna chamada UdpUse indica se o Windows Virtual Desktop RDP Stack utiliza o protocolo UDP na ligação atual do utilizador.
Os valores possíveis são:

* **0** - a ligação do utilizador não está a utilizar o Caminho-Curto RDP
* **1** - a ligação do utilizador está a utilizar o Caminho-Curto RDP
  
A seguinte lista de consultas permite-lhe rever as informações de ligação. Pode executar esta consulta no [editor de consulta do Log Analytics.](../azure-monitor/log-query/get-started-portal.md#write-and-run-basic-queries) Para cada consulta, `userupn` substitua-a pela UPN do utilizador que pretende procurar.

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated, UdpUse, SessionHostName, SessionHostSxSStackVersion
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId, UdpUse)
on CorrelationId
| project StartTime, Duration = EndTime - StartTime, ResourceAlias, UdpUse,  SessionHostName, SessionHostSxSStackVersion
| sort by StartTime asc
```

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="verify-shortpath-listener"></a>Verificar ouvinte do Shortpath

Para verificar se o ouvinte da UDP está ativado, utilize o seguinte comando PowerShell no anfitrião da sessão:

```powershell
Get-NetUDPEndpoint -OwningProcess ((Get-WmiObject win32_service -Filter "name = 'TermService'").ProcessId)  -LocalPort 3390
```

Se estiver ativado, verá a saída como a seguinte

```dos
LocalAddress                             LocalPort
------------                             ---------
::                                       3390
0.0.0.0                                  3390
```

Se houver um conflito, pode identificar o processo que ocupa a porta usando o seguinte comando

```powershell
Get-Process -id (Get-NetUDPEndpoint  -LocalPort 3390 -LocalAddress 0.0.0.0).OwningProcess
```

## <a name="disabling-rdp-shortpath"></a>Caminho-curto desativado RDP

Em alguns casos, poderá ter de desativar o transporte de RdP Shortpath. Pode desativar o Caminho Curto RDP utilizando a política de grupo.

### <a name="disabling-rdp-shortpath-on-the-client"></a>Desativar o Caminho Curto RDP no cliente

Para desativar o Caminho Curto RDP para um cliente específico, pode utilizar a seguinte Política de Grupo para desativar o suporte da UDP:

1. No cliente, Run **gpedit.msc**.
2. Navegue para **modelos de administração de > de configuração do computador > componentes do Windows > serviços de ambiente de trabalho remoto > cliente de ligação remota ao ambiente de trabalho**.
3. Defina a definição **"Desligar uDP on client"** para **Ativar**

### <a name="disabling-rdp-shortpath-on-the-session-host"></a>Desativando o Caminho Curto RDP no anfitrião da sessão

Para desativar o Caminho Curto RDP para um anfitrião de sessão específica, pode utilizar a seguinte Política de Grupo para desativar o suporte da UDP:

1. Na Sessão Host Run **gpedit.msc**.
2. Navegue para **modelos de administração de > de configuração do computador > componentes do Windows > serviços de ambiente de trabalho remoto > ligações > de ligação ao ambiente de trabalho remoto**.
3. Defina a definição **"Selecione PROTOCOLOS de Transporte RDP" apenas** para **TCP**

## <a name="public-preview-feedback"></a>Feedback de pré-visualização pública

Gostaríamos de ouvir sobre as suas experiências com esta pré-estreia pública!
* Para perguntas, pedidos, comentários e outros comentários, [utilize este formulário de feedback](https://aka.ms/RDPShortpathFeedback).

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a conectividade da rede virtual do Windows Desktop, consulte [a conectividade da rede virtual de desktop do Windows.](network-connectivity.md)
* Para começar com a Qualidade de Serviço (QoS) para o Windows Virtual Desktop, consulte [implementar qualidade de serviço (QoS) para Windows Virtual Desktop](rdp-quality-of-service-qos.md).
