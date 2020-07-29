---
title: Conexão de serviço de resolução de problemas Windows Virtual Desktop - Azure
description: Como resolver problemas quando configura as ligações com o cliente num ambiente de inquilino virtual do Windows Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 06/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 875f39653c5240e3a1b571b531eb2bb08c4811d0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289848"
---
# <a name="windows-virtual-desktop-service-connections"></a>Conexões de serviço de desktop virtual do Windows

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop com objetos de ambiente de trabalho virtuais do Windows, gestor de recursos do Azure. Se estiver a utilizar o Windows Virtual Desktop (clássico) sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md).

Utilize este artigo para resolver problemas com as ligações do cliente virtual do Windows Desktop.

## <a name="provide-feedback"></a>Enviar comentários

Pode dar-nos feedback e discutir o Serviço de Ambiente de Trabalho Virtual do Windows com a equipa de produtos e outros membros da comunidade ativa na [Comunidade virtual de Desktop Tech do Windows.](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>O utilizador conecta-se mas nada é apresentado (sem feed)

Um utilizador pode iniciar clientes remote desktop e é capaz de autenticar, no entanto o utilizador não vê nenhum ícone no feed de descoberta web.

1. Confirme que o utilizador que reporta os problemas foi atribuído aos grupos de aplicação utilizando esta linha de comando:

     ```powershell
     Get-AzRoleAssignment -SignInName <userupn>
     ```

2. Confirme se o utilizador está a iniciar sessão com as credenciais corretas.

3. Se o cliente web estiver a ser utilizado, confirme que não existem problemas de credenciais em cache.

4. Se o utilizador fizer parte de um grupo de utilizadores do Azure Ative Directory (AD), certifique-se de que o grupo de utilizador é um grupo de segurança em vez de um grupo de distribuição. O Windows Virtual Desktop não suporta grupos de distribuição Azure AD.

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral sobre a resolução de problemas do Windows Virtual Desktop e as faixas de escalada, consulte [a visão geral da resolução de problemas, o feedback e o suporte](troubleshoot-set-up-overview.md).
- Para resolver problemas enquanto cria um ambiente de ambiente de trabalho virtual do Windows e uma piscina de anfitrião num ambiente de ambiente de trabalho virtual do Windows, consulte [ambiente e a criação de piscinas de anfitriões.](troubleshoot-set-up-issues.md)
- Para resolver problemas enquanto configura uma máquina virtual (VM) no Windows Virtual Desktop, consulte a [configuração da máquina virtual do anfitrião da Sessão](troubleshoot-vm-configuration.md).
- Para resolver problemas ao utilizar o PowerShell com o Windows Virtual Desktop, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Para passar por um tutorial de resolução de [problemas, consulte Tutorial: Implementações de modelos do Gestor de Recursos de Resolução de Problemas](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
