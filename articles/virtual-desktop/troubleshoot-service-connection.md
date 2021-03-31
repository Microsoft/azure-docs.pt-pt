---
title: Conexão de serviço de resolução de problemas Windows Virtual Desktop - Azure
description: Como resolver problemas ao configurar ligações de serviço num ambiente de inquilino virtual do Windows Desktop.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 10/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 42502864cfed177adfe487e9c59247579628fec8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98539081"
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

## <a name="user-loses-existing-feed-and-no-remote-resource-is-displayed-no-feed"></a>O utilizador perde o feed existente e não é apresentado nenhum recurso remoto (sem feed)

Este erro geralmente aparece depois de um utilizador ter mudado a sua subscrição de um inquilino Azure AD para outro. Como resultado, o serviço perde o rasto das suas atribuições de utilizador, uma vez que estas ainda estão ligadas ao antigo inquilino da AD Azure.

Para resolver isto, tudo o que precisa de fazer é transferir os utilizadores para os seus grupos de aplicações.

Isto também pode acontecer se um Fornecedor CSP criou a subscrição e depois foi transferido para o cliente. Para resolver este reencamíssitão, o Fornecedor de Recursos.

1. Inicie sessão no portal do Azure.
2. Vá à **Subscrição** e, em seguida, selecione a sua subscrição.
3. No menu do lado esquerdo da página, selecione **Fornecedor de Recursos**.
4. Localizar e selecionar **Microsoft.DesktopVirtualization** e, em seguida, selecionar **Re-registrar**.

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral sobre a resolução de problemas do Windows Virtual Desktop e as faixas de escalada, consulte [a visão geral da resolução de problemas, o feedback e o suporte](troubleshoot-set-up-overview.md).
- Para resolver problemas enquanto cria um ambiente de ambiente de trabalho virtual do Windows e uma piscina de anfitrião num ambiente de ambiente de trabalho virtual do Windows, consulte [ambiente e a criação de piscinas de anfitriões.](troubleshoot-set-up-issues.md)
- Para resolver problemas enquanto configura uma máquina virtual (VM) no Windows Virtual Desktop, consulte a [configuração da máquina virtual do anfitrião da Sessão](troubleshoot-vm-configuration.md).
- Para resolver problemas relacionados com o agente virtual do Windows desktop ou conectividade de sessão, consulte [problemas comuns](troubleshoot-agent.md)de Computador de Secretária virtual do Windows .
- Para resolver problemas ao utilizar o PowerShell com o Windows Virtual Desktop, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Para passar por um tutorial de resolução de [problemas, consulte Tutorial: Implementações de modelos do Gestor de Recursos de Resolução de Problemas](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
