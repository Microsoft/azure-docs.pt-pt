---
title: Windows Ambiente de Trabalho Virtual - Azure
description: Os elementos básicos de um ambiente de ambiente de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d666f7e3e43b5429423a5356aa00e074ed020869
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82615334"
---
# <a name="windows-virtual-desktop-environment"></a>Ambiente do Windows Virtual Desktop

>[!IMPORTANT]
>Este conteúdo aplica-se à versão outono 2019 que não suporta objetos de ambiente de trabalho virtuais do Gestor de Recursos Azure. Se está a tentar gerir os objetos virtuais do Azure Resource Manager Windows, introduzidos na atualização da primavera de 2020, consulte [este artigo](../environment-setup.md).

O Windows Virtual Desktop é um serviço que dá aos utilizadores um acesso fácil e seguro aos seus desktops virtualizados e RemoteApps. Este tópico irá dizer-lhe um pouco mais sobre a estrutura geral do ambiente de trabalho virtual do Windows.

## <a name="tenants"></a>Inquilinos

O inquilino virtual do Windows Desktop é a interface principal para gerir o seu ambiente de trabalho virtual do Windows. Cada inquilino virtual do Windows desktop deve estar associado ao Diretório Ativo Azure contendo os utilizadores que irão iniciar súmito no ambiente. A partir do inquilino virtual do Windows Desktop, você pode começar a criar piscinas de anfitriões para executar as cargas de trabalho dos seus utilizadores.

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

- Um utilizador não pode ser designado para um grupo de aplicações de desktop e um grupo de aplicações RemoteApp no mesmo pool de anfitriões.
- Um utilizador pode ser designado para vários grupos de aplicações dentro da mesma piscina de anfitriões, e o seu feed será uma acumulação de ambos os grupos de aplicações.

## <a name="tenant-groups"></a>Grupos de inquilinos

No Windows Virtual Desktop, o inquilino virtual do Windows desktop é onde a maioria da configuração e configuração acontece. O inquilino virtual do Windows Desktop contém as piscinas de anfitrião, grupos de aplicações e atribuições de utilizadores do grupo de aplicações. No entanto, pode haver certas situações em que você precisa gerir vários inquilinos do Windows Virtual Desktop de uma só vez, especialmente se você é um Fornecedor de Serviços cloud (CSP) ou um parceiro de hospedagem. Nestas situações, você pode usar um grupo de inquilinos virtual do Windows Virtual desktop personalizado para colocar cada um dos inquilinos virtual do Windows Desktop dos clientes e gerir centralmente o acesso. No entanto, se você está apenas gerindo um único inquilino virtual Windows Desktop, o conceito de grupo de inquilinos não se aplica e você pode continuar a operar e gerir seu inquilino que existe no grupo de inquilinos padrão.

## <a name="end-users"></a>Utilizadores finais

Depois de atribuir os utilizadores aos seus grupos de aplicações, podem ligar-se a uma implementação virtual do Windows Desktop com qualquer um dos clientes do Windows Virtual Desktop.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o acesso delegado e como atribuir funções aos utilizadores no [Acesso Delegado no Windows Virtual Desktop](delegated-access-virtual-desktop-2019.md).

Para aprender a configurar o seu inquilino virtual windows Desktop, consulte [Criar um inquilino no Windows Virtual Desktop](tenant-setup-azure-active-directory.md).

Para saber como ligar ao Windows Virtual Desktop, consulte um dos seguintes artigos:

- [Ligar a partir do Windows 10 ou Windows 7](../connect-windows-7-and-10.md)
- [Ligar a partir de um browser web](connect-web-2019.md)
