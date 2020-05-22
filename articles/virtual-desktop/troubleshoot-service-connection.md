---
title: Ligação de serviço de resolução de problemas Windows Virtual Desktop - Azure
description: Como resolver problemas quando configura as ligações do cliente num ambiente de inquilino sinuoso virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7bf05fe039de2ab9e25495f9e2652fde8fac34e1
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747703"
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

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral sobre a resolução de problemas do Windows Virtual Desktop e as faixas de escalada, consulte a [visão geral, feedback e suporte](troubleshoot-set-up-overview.md)de Resolução de Problemas.
- Para resolver problemas ao criar um ambiente de ambiente de trabalho virtual windows e piscina de hospedar num ambiente de ambiente de trabalho virtual windows, consulte Ambiente e criação de [piscina anfitrião.](troubleshoot-set-up-issues.md)
- Para resolver problemas ao configurar uma máquina virtual (VM) no Windows Virtual Desktop, consulte a [configuração virtual](troubleshoot-vm-configuration.md)do anfitrião da sessão .
- Para resolver problemas ao utilizar o PowerShell com o Windows Virtual Desktop, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Para passar por um tutorial de resolução de [problemas, consulte Tutorial: Implementações](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)do modelo de gestor de recursos de resolução de problemas .
