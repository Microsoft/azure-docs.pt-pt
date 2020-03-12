---
title: Ligação de serviço de resolução de problemas Windows Virtual Desktop - Azure
description: Como resolver problemas quando configura as ligações do cliente num ambiente de inquilino sinuoso virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 57d5198cb54dc096fb09bb52d76539b1e4bbc1f2
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127456"
---
# <a name="windows-virtual-desktop-service-connections"></a>Conexões de serviço de desktop virtual do Windows

Utilize este artigo para resolver problemas com ligações ao cliente do Windows Virtual Desktop.

## <a name="provide-feedback"></a>Enviar comentários

Pode dar-nos feedback e discutir o Serviço de Ambiente de Trabalho Virtual do Windows com a equipa de produtos e outros membros ativos da comunidade no [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>O utilizador liga-se mas nada é exibido (sem alimentação)

Um utilizador pode iniciar clientes do Remote Desktop e é capaz de autenticar, no entanto o utilizador não vê nenhum ícone no feed de descoberta web.

Confirme que o utilizador que reporta os problemas foi atribuído aos grupos de aplicação utilizando esta linha de comando:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Confirme que o utilizador está a fazer login com as credenciais corretas.

Se o cliente da web estiver a ser utilizado, confirme que não existem problemas de credenciais em cache.

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Máquinas virtuais multissessões do Windows 10 Enterprise não respondem

Se uma máquina virtual não responder e não conseguir aceder através de RDP, terá de a resolver com a funcionalidade de diagnóstico, verificando o estado do hospedeiro.

Para verificar o estado do anfitrião, execute este cmdlet:

```powershell
Get-RdsSessionHost -TenantName $TenantName -HostPoolName $HostPool | ft SessionHostName, LastHeartBeat, AllowNewSession, Status
```

Se o estado do anfitrião for `NoHeartBeat`, isso significa que o VM não está a responder e o agente não pode comunicar com o serviço de ambiente de trabalho virtual do Windows.

```powershell
SessionHostName          LastHeartBeat     AllowNewSession    Status 
---------------          -------------     ---------------    ------ 
WVDHost1.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost2.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost3.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost4.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost5.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
```

Há algumas coisas que pode fazer para corrigir o estado NoHeartBeat.

### <a name="update-fslogix"></a>Atualizar FSLogix

Se o seu FSLogix não estiver atualizado, especialmente se for versão 2.9.7205.27375 de frxdrvvt.sys, pode causar um impasse. Certifique-se de [atualizar o FSLogix para a versão mais recente](https://go.microsoft.com/fwlink/?linkid=2084562).

### <a name="disable-bgtaskregistrationmaintenancetask"></a>Desativar a Tarefa de Manutenção de Registos bgTask

Se a atualização do FSLogix não funcionar, o problema pode ser que um componente BiSrv esteja a esgotar os recursos do sistema durante uma tarefa de manutenção semanal. Desative temporariamente a tarefa de manutenção desativando a Tarefa de Manutenção de Registo seletiva da BgTask com um destes dois métodos:

- Vá ao menu Iniciar e procure o Agendador de **Tarefas**. Navegue para a Biblioteca do Programador de **Tarefas** > **Microsoft** > **Infraestrutura**de corretor estoiro **do Windows** > . Procure uma tarefa chamada **BgTaskRegistrationMaintenanceTask**. Quando o encontrar, clique-o à direita e selecione **Desativar** a partir do menu suspenso.
- Abra um menu de linha de comando como administrador e execute o seguinte comando:
    
    ```cmd
    schtasks /change /tn "\Microsoft\Windows\BrokerInfrastructure\BgTaskRegistrationMaintenanceTask" /disable 
    ```

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral sobre a resolução de problemas do Windows Virtual Desktop e as faixas de escalada, consulte a [visão geral, feedback e suporte](troubleshoot-set-up-overview.md)de Resolução de Problemas.
- Para resolver problemas ao criar um inquilino e uma piscina de hospedas num ambiente de ambiente de trabalho virtual windows, consulte [tenant e host pool creation](troubleshoot-set-up-issues.md).
- Para resolver problemas ao configurar uma máquina virtual (VM) no Windows Virtual Desktop, consulte a [configuração virtual](troubleshoot-vm-configuration.md)do anfitrião da sessão .
- Para resolver problemas ao utilizar o PowerShell com o Windows Virtual Desktop, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Para passar por um tutorial de resolução de [problemas, consulte Tutorial: Implementações](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)do modelo de gestor de recursos de resolução de problemas .
