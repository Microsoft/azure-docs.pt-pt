---
title: Ambiente de ambiente de trabalho virtual windows - Azure
description: Os elementos básicos de um ambiente de ambiente de trabalho virtual windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 33d058f028b7032f296ffcf82f0e5fe2c993e6fb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79127914"
---
# <a name="windows-virtual-desktop-environment"></a>Ambiente do Windows Virtual Desktop

O Windows Virtual Desktop é um serviço que dá aos utilizadores acesso fácil e seguro aos seus desktops e RemoteApps virtualizados. Este tópico irá dizer-lhe um pouco mais sobre a estrutura geral do ambiente de ambiente de trabalho virtual do Windows.

## <a name="tenants"></a>Inquilinos

O inquilino do Windows Virtual Desktop é a interface principal para gerir o seu ambiente de trabalho virtual windows. Cada inquilino do Windows Virtual Desktop deve estar associado ao Diretório Ativo Azure contendo os utilizadores que irão iniciar sessão no ambiente. A partir do inquilino do Windows Virtual Desktop, você pode começar a criar piscinas de hospedas para executar as cargas de trabalho dos seus utilizadores.

## <a name="host-pools"></a>Piscinas de acolhimento

Um pool de anfitriões é uma coleção de máquinas virtuais Azure que se registam no Windows Virtual Desktop como anfitriões de sessão quando executa o agente de ambiente de trabalho virtual do Windows. Todas as máquinas virtuais de hospedagem de sessão numa piscina de anfitriões devem ser provenientes da mesma imagem para uma experiência consistente do utilizador.

Uma piscina de acolhimento pode ser um de dois tipos:

- Pessoal, onde cada anfitrião de sessão é atribuído a utilizadores individuais.
- Em conjunto, onde os anfitriões da sessão podem aceitar ligações de qualquer utilizador autorizado a um grupo de aplicações dentro da piscina anfitriã.

Você pode definir propriedades adicionais no pool anfitrião para alterar o seu comportamento de equilíbrio de carga, quantas sessões cada anfitrião de sessão pode tomar, e o que o utilizador pode fazer para sessão de anfitriões na piscina anfitriã enquanto está inscrito nas suas sessões de ambiente de trabalho virtual do Windows. Controla os recursos publicados aos utilizadores através de grupos de aplicações.

## <a name="app-groups"></a>Grupos de aplicações

Um grupo de aplicações é um agrupamento lógico de aplicações instaladas em anfitriões de sessão na piscina anfitriã. Um grupo de aplicações pode ser um de dois tipos:

- RemoteApp, onde os utilizadores acedem aos RemoteApps seleciona e publica individualmente para o grupo de aplicações
- Ambiente de trabalho, onde os utilizadores acedem ao ambiente de trabalho completo

Por padrão, um grupo de aplicações de ambiente de trabalho (chamado "Desktop Application Group") é automaticamente criado sempre que cria um pool de anfitriões. Pode remover este grupo de aplicações a qualquer momento. No entanto, não é possível criar outro grupo de aplicações para desktop na piscina de anfitriões enquanto existe um grupo de aplicações para desktop. Para publicar RemoteApps, tem de criar um grupo de aplicações RemoteApp. Pode criar vários grupos de aplicações RemoteApp para acomodar diferentes cenários de trabalhadores. Diferentes grupos de aplicações RemoteApp também podem conter aplicações remotas sobrepostas.

Para publicar recursos aos utilizadores, deve atribuí-los a grupos de aplicações. Ao atribuir os utilizadores a grupos de aplicações, considere as seguintes coisas:

- Um utilizador não pode ser atribuído tanto a um grupo de aplicações para desktop como a um grupo de aplicações RemoteApp na mesma piscina de anfitriões.
- Um utilizador pode ser atribuído a vários grupos de aplicações dentro da mesma piscina de anfitriões, e o seu feed será uma acumulação de ambos os grupos de aplicações.

## <a name="tenant-groups"></a>Grupos de inquilinos

No Windows Virtual Desktop, o inquilino do Windows Virtual Desktop é onde acontece a maior parte da configuração e configuração. O inquilino do Windows Virtual Desktop contém as piscinas de anfitriões, grupos de aplicações e atribuições de utilizadores do grupo de aplicações. No entanto, pode haver algumas situações em que você precisa gerir vários inquilinos do Windows Virtual Desktop de uma só vez, especialmente se você é um Fornecedor de Serviços cloud (CSP) ou um parceiro de hospedagem. Nestas situações, você pode usar um grupo de inquilinos personalizados do Windows Virtual Desktop para colocar cada um dos inquilinos do Windows Virtual Desktop dos clientes e gerir centralmente o acesso. No entanto, se você está apenas gerindo um único inquilino windows virtual desktop, o conceito do grupo de inquilinos não se aplica e você pode continuar a operar e gerir o seu inquilino que existe no grupo de inquilinos padrão.

## <a name="end-users"></a>Utilizadores finais

Depois de ter atribuído os utilizadores aos seus grupos de aplicações, estes podem ligar-se a uma implementação do Windows Virtual Desktop com qualquer um dos clientes do Windows Virtual Desktop.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o acesso delegado e como atribuir funções aos utilizadores [no Acesso Delegado no Windows Virtual Desktop](delegated-access-virtual-desktop.md).

Para aprender a configurar o seu inquilino de ambiente de trabalho virtual Windows, consulte [Criar um inquilino no Windows Virtual Desktop](tenant-setup-azure-active-directory.md).

Para aprender a ligar-se ao Windows Virtual Desktop, consulte um dos seguintes artigos:

- [Ligar a partir do Windows 10 ou Windows 7](connect-windows-7-and-10.md)
- [Ligar a partir de um browser web](connect-web.md)
