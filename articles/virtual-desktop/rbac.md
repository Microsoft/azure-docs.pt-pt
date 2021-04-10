---
title: Funções incorporadas Windows Virtual Desktop - Azure
description: Uma visão geral das funções incorporadas para o Windows Virtual Desktop disponível para O RBAC Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1120db685c54ff062f03aca9002bf77af549bc26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582124"
---
# <a name="built-in-roles-for-windows-virtual-desktop"></a>Funções incorporadas para desktop virtual do Windows

O Windows Virtual Desktop utiliza controlos de acesso baseados em funções (RBAC) para atribuir funções aos utilizadores e administradores. Estas funções dão permissão aos administradores para executarem certas tarefas. Para saber mais sobre papéis incorporados para o Azure RBAC, consulte [as funções incorporadas do Azure.](../role-based-access-control/built-in-roles.md)

As funções integradas padrão para a Azure são Proprietário, Colaborador e Leitor. No entanto, o Windows Virtual Desktop tem funções adicionais que permitem separar funções de gestão para piscinas de anfitriões, grupos de aplicações e espaços de trabalho. Esta separação permite-lhe ter mais controlo granular sobre as tarefas administrativas. Estas funções são nomeadas em conformidade com as funções padrão da Azure e a metodologia de menor privilégio.

O Windows Virtual Desktop não tem uma função específica do Proprietário. No entanto, pode utilizar uma função padrão do Proprietário para os objetos de serviço.

## <a name="desktop-virtualization-contributor"></a>Colaborador de Virtualização do Ambiente de Trabalho

A função de Contribuinte de Virtualização desktop permite-lhe gerir todos os aspetos da implementação. No entanto, não lhe dá acesso a recursos computacional. Também vai precisar da função de Administrador de Acesso ao Utilizador para publicar grupos de aplicações para utilizadores ou grupos de utilizadores.


- Microsoft.DesktopVirtualization/\* 
- Microsoft.Resources/subscrições/resourceGroups/read
- Microsoft.Resources/implementações/\*
- Microsoft.Autorização/ \* /ler
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="desktop-virtualization-reader"></a>Leitor de Virtualização de Desktop

O papel de Leitor de Virtualização desktop permite-lhe ver tudo na implementação, mas não permite que faça alterações.

- Microsoft.DesktopVirtualization/ \* /read
- Microsoft.Resources/subscrições/resourceGroups/read
- Microsoft.Resources/implementações/ler
- Microsoft.Autorização/ \* /ler
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="host-pool-contributor"></a>Colaborador do Pool Host

O papel de Colaborador do Pool Host permite-lhe gerir todos os aspetos das piscinas hospedeiras, incluindo o acesso aos recursos. Você precisará de um papel de contribuinte extra, Contribuinte máquina virtual, para criar máquinas virtuais. Você precisará de funções de contribuinte do AppGroup e do Workspace para criar o pool anfitrião usando o portal ou pode usar o papel de Contribuinte de Virtualização desktop.

A lista a seguir descreve quais as permissões a que esta função pode aceder:

- Microsoft.DesktopVirtualization/hostpools/\*
- Microsoft.Resources/subscrições/resourceGroups/read
- Microsoft.Resources/implementações/\*
- Microsoft.Autorização/ \* /ler
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="host-pool-reader"></a>Leitor de Piscinas anfitrião

O papel host Pool Reader permite-lhe ver tudo na piscina de anfitriões, mas não lhe permite fazer alterações.

- Microsoft.DesktopVirtualization/hostpools/ \* /read
- Microsoft.Resources/subscrições/resourceGroups/read
- Microsoft.Resources/implementações/ler
- Microsoft.Autorização/ \* /ler
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="application-group-contributor"></a>Contribuinte do Grupo de Candidatura

A função contribuidora do Grupo de Aplicações permite-lhe gerir todos os aspetos dos grupos de aplicações. Se pretender publicar grupos de aplicações para utilizadores ou grupos de utilizadores, precisará da função de Administrador de Acesso ao Utilizador.

A lista a seguir descreve quais as permissões a que esta função pode aceder:

- Microsoft.DesktopVirtualization/applicationgroups/\*
- Microsoft.DesktopVirtualization/hostpools/read
- Microsoft.DesktopVirtualization/hostpools/sessionhosts/read
- Microsoft.Resources/subscrições/resourceGroups/read
- Microsoft.Resources/implementações/\*
- Microsoft.Autorização/ \* /ler
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="application-group-reader"></a>Leitor de Grupo de Aplicação

A função De Leitor de Grupo de Aplicações permite-lhe ver tudo no grupo de aplicações e não lhe permitirá fazer quaisquer alterações.

A lista a seguir descreve quais as permissões a que esta função pode aceder:

- Microsoft.DesktopVirtualization/applicationgroups/ \* /read
- Microsoft.DesktopVirtualization/applicationgroups/read
- Microsoft.DesktopVirtualization/hostpools/read
- Microsoft.DesktopVirtualization/hostpools/sessionhosts/read
- Microsoft.Resources/subscrições/resourceGroups/read
- Microsoft.Resources/implementações/ler
- Microsoft.Autorização/ \* /ler
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="workspace-contributor"></a>Colaborador do Espaço de Trabalho

O papel de Colaborador do Espaço de Trabalho permite-lhe gerir todos os aspetos dos espaços de trabalho. Para obter informações sobre aplicações adicionadas aos grupos de aplicações, também terá de ser atribuída a função de Leitor de Grupo de Aplicação.

A lista a seguir descreve quais as permissões a que esta função pode aceder:

- Microsoft.DesktopVirtualization/workspaces/\*
- Microsoft.DesktopVirtualization/applicationgroups/read
- Microsoft.Resources/subscrições/resourceGroups/read
- Microsoft.Resources/implementações/\*
- Microsoft.Autorização/ \* /ler
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="workspace-reader"></a>Leitor de espaço de trabalho

O papel de Workspace Reader permite-lhe ver tudo no espaço de trabalho, mas não lhe permite fazer alterações.

A lista a seguir descreve quais as permissões a que esta função pode aceder:

- Microsoft.DesktopVirtualization/workspaces/read
- Microsoft.DesktopVirtualization/applicationgroups/read
- Microsoft.Resources/subscrições/resourceGroups/read
- Microsoft.Resources/implementações/ler
- Microsoft.Autorização/ \* /ler
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="user-session-operator"></a>Operador de sessão de utilizador

A função 'Operador de Sessão de Utilizador' permite-lhe enviar mensagens, desligar sessões e utilizar a função "logoff" para assinar sessões fora do anfitrião da sessão. No entanto, este papel não permite que execute a gestão do anfitrião da sessão como remover o anfitrião da sessão, mudar o modo de drenagem, e assim por diante. Este papel pode ver atribuições, mas não pode modificar administradores. Recomendamos que atribua este papel a piscinas hospedeiras específicas. Se você der esta permissão a nível de grupo de recursos, o administrador terá lido permissão em todas as piscinas hospedeiras sob um grupo de recursos.

A lista a seguir descreve quais as permissões a que esta função pode aceder:

- Microsoft.DesktopVirtualization/hostpools/read
- Microsoft.DesktopVirtualization/hostpools/sessionhosts/read
- Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/\*
- Microsoft.Resources/subscrições/resourceGroups/read
- Microsoft.Resources/implementações/ler
- Microsoft.Autorização/ \* /ler
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="session-host-operator"></a>Operador anfitrião de sessão

A função 'Operador anfitrião da sessão' permite-lhe visualizar e remover os anfitriões da sessão, bem como alterar o modo de drenagem. Não podem adicionar anfitriões de sessão usando o portal Azure porque não têm permissão de escrita para objetos de piscina hospedeiras. Se o token de registo for válido (gerado e não expirado), pode utilizar esta função para adicionar anfitriões de sessão à piscina anfitriã fora do portal Azure se o administrador tiver permissões de computação através da função Contribuinte máquina virtual.

A lista a seguir descreve quais as permissões a que esta função pode aceder:

- Microsoft.DesktopVirtualization/hostpools/read
- Microsoft.DesktopVirtualization/hostpools/sessionhosts/\*
- Microsoft.Resources/subscrições/resourceGroups/read
- Microsoft.Resources/implementações/ler
- Microsoft.Autorização/ \* /ler
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*
