---
title: Windows Ambiente de Trabalho Virtual - Azure
description: Conheça os elementos básicos de um ambiente de desktop virtual do Windows, como piscinas de anfitriões e grupos de aplicações.
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 99a07dd1791b539ea44fcbab250aa9c227ee1705
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88002620"
---
# <a name="windows-virtual-desktop-environment"></a>Ambiente do Windows Virtual Desktop

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop com objetos de ambiente de trabalho virtuais do Windows, gestor de recursos do Azure. Se estiver a utilizar o Windows Virtual Desktop (clássico) sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/environment-setup-2019.md).

O Windows Virtual Desktop é um serviço que dá aos utilizadores um acesso fácil e seguro aos seus desktops virtualizados e RemoteApps. Este tópico irá dizer-lhe um pouco mais sobre a estrutura geral do ambiente de trabalho virtual do Windows.

## <a name="host-pools"></a>Piscinas de acolhimento

Uma piscina de anfitriões é uma coleção de máquinas virtuais Azure que se registam no Windows Virtual Desktop como anfitriões de sessão quando executam o agente virtual do Windows Desktop. Todas as máquinas virtuais hospedeiras de sessão numa piscina de anfitrião devem ser provenientes da mesma imagem para uma experiência consistente do utilizador.

Uma piscina de anfitriões pode ser um de dois tipos:

- Pessoal, onde cada anfitrião de sessão é atribuído a utilizadores individuais.
- Em conjunto, onde os anfitriões da sessão podem aceitar ligações de qualquer utilizador autorizado a um grupo de aplicações dentro da piscina de anfitriões.

Pode definir propriedades adicionais na piscina anfitriã para alterar o seu comportamento de equilíbrio de carga, quantas sessões cada anfitrião pode fazer e o que o utilizador pode fazer aos anfitriões da sessão na piscina de anfitriões enquanto se inscreve nas suas sessões de Desktop Virtual do Windows. Controla os recursos publicados aos utilizadores através de grupos de aplicações.

## <a name="app-groups"></a>Grupos de aplicações

Um grupo de aplicações é um agrupamento lógico de aplicações instaladas em anfitriões de sessão na piscina anfitriã. Um grupo de aplicações pode ser um de dois tipos:

- RemoteApp, onde os utilizadores acedem aos RemoteApps, seleciona e publica individualmente para o grupo de aplicações
- Desktop, onde os utilizadores acedem ao ambiente de trabalho completo

Por predefinição, um grupo de aplicações de desktop (chamado "Desktop Application Group") é automaticamente criado sempre que cria um pool de anfitriões. Pode remover este grupo de aplicações a qualquer momento. No entanto, não é possível criar outro grupo de aplicações para desktop na piscina de anfitriões enquanto existe um grupo de aplicações para desktop. Para publicar RemoteApps, tem de criar um grupo de aplicações RemoteApp. Pode criar vários grupos de aplicações RemoteApp para acomodar diferentes cenários de trabalhadores. Diferentes grupos de aplicações RemoteApp também podem conter a sobreposição de RemoteApps.

Para publicar recursos aos utilizadores, deve atribuí-los a grupos de aplicações. Ao atribuir utilizadores a grupos de aplicações, considere as seguintes coisas:

- Um utilizador pode ser designado para um grupo de aplicações de desktop e um grupo de aplicações RemoteApp no mesmo pool de anfitriões. No entanto, os utilizadores só podem lançar um tipo de grupo de aplicações por sessão. Os utilizadores não podem lançar os dois tipos de grupos de aplicações ao mesmo tempo numa única sessão.
- Um utilizador pode ser designado para vários grupos de aplicações dentro da mesma piscina de anfitriões, e o seu feed será uma acumulação de ambos os grupos de aplicações.

## <a name="workspaces"></a>Áreas de Trabalho

Um espaço de trabalho é um agrupamento lógico de grupos de aplicações no Windows Virtual Desktop. Cada grupo de aplicações virtual do Windows Desktop deve estar associado a um espaço de trabalho para os utilizadores verem as aplicações remotas e os ambientes de trabalho que lhes são publicados.

## <a name="end-users"></a>Utilizadores finais

Depois de atribuir os utilizadores aos seus grupos de aplicações, podem ligar-se a uma implementação virtual do Windows Desktop com qualquer um dos clientes do Windows Virtual Desktop.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o acesso delegado e como atribuir funções aos utilizadores no [Acesso Delegado no Windows Virtual Desktop](delegated-access-virtual-desktop.md).

Para aprender a configurar a sua piscina de anfitriões virtual do Windows Desktop, consulte [criar uma piscina de anfitriões com o portal Azure.](create-host-pools-azure-marketplace.md)

Para saber como ligar ao Windows Virtual Desktop, consulte um dos seguintes artigos:

- [Ligue-se ao Windows 10 ou ao Windows 7](connect-windows-7-10.md)
- [Conecte-se com um navegador web](connect-web.md)
- [Ligar ao cliente Android](connect-android.md)
- [Ligar ao cliente de macOS](connect-macos.md)
- [Ligar ao cliente de iOS](connect-ios.md)