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
ms.openlocfilehash: a6298b3a9c5769b1d82f89956736b451935b2c5d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612644"
---
# <a name="windows-virtual-desktop-service-connections"></a>Conexões de serviço de desktop virtual do Windows

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização da primavera de 2020 com os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows. Se estiver a utilizar o lançamento do Windows Virtual Desktop Fall 2019 sem objetos do Gestor de Recursos Azure, consulte [este artigo](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md).
>
> A atualização Do Windows Virtual Desktop Spring 2020 encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos usá-la para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

Utilize este artigo para resolver problemas com ligações ao cliente do Windows Virtual Desktop.

## <a name="provide-feedback"></a>Enviar comentários

Pode dar-nos feedback e discutir o Serviço de Ambiente de Trabalho Virtual do Windows com a equipa de produtos e outros membros ativos da comunidade no [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>O utilizador liga-se mas nada é exibido (sem alimentação)

Um utilizador pode iniciar clientes do Remote Desktop e é capaz de autenticar, no entanto o utilizador não vê nenhum ícone no feed de descoberta web.

Confirme que o utilizador que reporta os problemas foi atribuído aos grupos de aplicação utilizando esta linha de comando:

```PowerShell
Get-AzRoleAssignment -SignInName <userupn>
```

Confirme que o utilizador está a iniciar sessão com as credenciais corretas.

Se o cliente da web estiver a ser utilizado, confirme que não existem problemas de credenciais em cache.

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Máquinas virtuais multissessões do Windows 10 Enterprise não respondem

Se uma máquina virtual não responder e não conseguir aceder através de RDP, terá de a resolver com a funcionalidade de diagnóstico, verificando o estado do hospedeiro.

Para verificar o estado do anfitrião, execute este cmdlet:

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -ResourceGroupName <resourcegroupname>| Format-List Name, LastHeartBeat, AllowNewSession, Status
```

Se o estado `NoHeartBeat`do anfitrião for , isso significa que o VM não está a responder e o agente não pode comunicar com o serviço de ambiente de trabalho virtual do Windows.

```powershell
Name            : 0301HP/win10pd-0.contoso.com 
LastHeartBeat   : 4/8/2020 1:48:35 AM 
AllowNewSession : True 
Status          : Available 

Name            : 0301HP/win10pd-1.contoso.com 
LastHeartBeat   : 4/8/2020 1:45:44 AM 
AllowNewSession : True 
Status          : NoHeartBeat
```

Há algumas coisas que pode fazer para corrigir o estado NoHeartBeat.

### <a name="update-fslogix"></a>Atualizar FSLogix

Se o seu FSLogix não estiver atualizado, especialmente se for versão 2.9.7205.27375 de frxdrvvt.sys, pode causar um impasse. Certifique-se de [atualizar o FSLogix para a versão mais recente](https://go.microsoft.com/fwlink/?linkid=2084562).

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral sobre a resolução de problemas do Windows Virtual Desktop e as faixas de escalada, consulte a [visão geral, feedback e suporte](troubleshoot-set-up-overview.md)de Resolução de Problemas.
- Para resolver problemas ao criar um ambiente de ambiente de trabalho virtual windows e piscina de hospedar num ambiente de ambiente de trabalho virtual windows, consulte Ambiente e criação de [piscina anfitrião.](troubleshoot-set-up-issues.md)
- Para resolver problemas ao configurar uma máquina virtual (VM) no Windows Virtual Desktop, consulte a [configuração virtual](troubleshoot-vm-configuration.md)do anfitrião da sessão .
- Para resolver problemas ao utilizar o PowerShell com o Windows Virtual Desktop, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Para passar por um tutorial de resolução de [problemas, consulte Tutorial: Implementações](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)do modelo de gestor de recursos de resolução de problemas .
