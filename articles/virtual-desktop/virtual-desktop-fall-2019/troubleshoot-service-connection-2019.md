---
title: Conexão de serviço de resolução de problemas Windows Virtual Desktop Fall 2019 - Azure
description: Como resolver problemas quando configura as ligações com o cliente num ambiente de inquilino virtual do Windows Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ad5e740a2874d398f3947fe755024bedc897ec73
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87085797"
---
# <a name="troubleshoot-windows-virtual-desktop-service-connections"></a>Resolução de problemas Ligações de serviço de desktop virtual do Windows

>[!IMPORTANT]
>Este conteúdo aplica-se à versão outono 2019 que não suporta objetos de ambiente de trabalho virtuais do Gestor de Recursos Azure. Se está a tentar gerir os objetos virtuais do Azure Resource Manager Windows, introduzidos na atualização da primavera de 2020, consulte [este artigo](../troubleshoot-service-connection.md).

Utilize este artigo para resolver problemas com as ligações do cliente virtual do Windows Desktop.

## <a name="provide-feedback"></a>Enviar comentários

Pode dar-nos feedback e discutir o Serviço de Ambiente de Trabalho Virtual do Windows com a equipa de produtos e outros membros da comunidade ativa na [Comunidade virtual de Desktop Tech do Windows.](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>O utilizador conecta-se mas nada é apresentado (sem feed)

Um utilizador pode iniciar clientes remote desktop e é capaz de autenticar, no entanto o utilizador não vê nenhum ícone no feed de descoberta web.

Confirme que o utilizador que reporta os problemas foi atribuído aos grupos de aplicação utilizando esta linha de comando:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Confirme se o utilizador está a iniciar sessão com as credenciais corretas.

Se o cliente web estiver a ser utilizado, confirme que não existem problemas de credenciais em cache.

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral sobre a resolução de problemas do Windows Virtual Desktop e as faixas de escalada, consulte [a visão geral da resolução de problemas, o feedback e o suporte](troubleshoot-set-up-overview-2019.md).
- Para resolver problemas enquanto cria um inquilino e piscina de hospedagem em um ambiente de desktop virtual windows, consulte [o Inquilino e a criação de piscina de anfitrião.](troubleshoot-set-up-issues-2019.md)
- Para resolver problemas enquanto configura uma máquina virtual (VM) no Windows Virtual Desktop, consulte a [configuração da máquina virtual do anfitrião da Sessão](troubleshoot-vm-configuration-2019.md).
- Para resolver problemas ao utilizar o PowerShell com o Windows Virtual Desktop, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell-2019.md).
- Para passar por um tutorial de resolução de [problemas, consulte Tutorial: Implementações de modelos do Gestor de Recursos de Resolução de Problemas](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
