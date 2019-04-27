---
title: Ambiente de pré-visualização de ambiente de Trabalho Virtual do Windows - Azure
description: Os elementos básicos de um ambiente de pré-visualização de ambiente de Trabalho Virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: ceed6a8bb74206b7c6689ce542482148800e4ba9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870324"
---
# <a name="windows-virtual-desktop-preview-environment"></a>Ambiente de pré-visualização de ambiente de Trabalho Virtual do Windows

Visualização de ambiente de Trabalho Virtual do Windows é um serviço que dá aos usuários um acesso fácil e seguro aos seus ambientes de trabalho virtualizados e RemoteApps. Este tópico informa um pouco mais sobre a estrutura geral do ambiente de área de Trabalho Virtual do Windows.

## <a name="tenants"></a>Inquilinos

O inquilino de área de Trabalho Virtual do Windows é a interface principal para o gerenciamento de seu ambiente de área de Trabalho Virtual do Windows. Cada inquilino de área de Trabalho Virtual do Windows tem de estar associado com o Azure Active Directory que contém os utilizadores que irão iniciar sessão no ambiente. No inquilino a área de Trabalho Virtual do Windows, pode começar a criar conjuntos de anfitrião para executar cargas de trabalho dos seus utilizadores.

## <a name="host-pools"></a>Conjuntos de anfitrião

Um agrupamento de host é uma coleção de máquinas virtuais do Azure que se registar para a área de Trabalho Virtual do Windows como hosts de sessão ao executar o agente de área de Trabalho Virtual do Windows. Todas as máquinas virtuais do anfitrião de sessão de um conjunto de anfitrião deve ter origem na mesma imagem para uma experiência de usuário consistente.

Um conjunto de anfitrião pode ser um dos dois tipos:

- Pessoal, onde cada anfitrião de sessões é atribuída a utilizadores individuais.
- Em pool, onde os hosts de sessão podem aceitar ligações a partir de qualquer utilizador autorizado a um grupo de aplicações dentro do conjunto de anfitrião.

Pode definir propriedades adicionais sobre o conjunto de anfitrião para alterar seu comportamento de balanceamento de carga, como muitas sessões, cada anfitrião de sessões pode tomar, e o que o utilizador pode fazer para hosts de sessão no agrupamento de anfitrião, enquanto tiver sessão iniciada no suas sessões de área de Trabalho Virtual do Windows. Controlar os recursos publicados para utilizadores através de grupos de aplicações.

## <a name="app-groups"></a>Grupos de aplicações

Um grupo de aplicações é um agrupamento lógico de aplicativos instalados em hosts de sessão no agrupamento de anfitrião. Um grupo de aplicações pode ser um dos dois tipos:

- RemoteApp, onde os utilizadores acedem a RemoteApps selecionar individualmente e publicar para o grupo de aplicações
- Ambiente de trabalho, onde os utilizadores aceder a área de trabalho completa

Por predefinição, um grupo de aplicativo de desktop (com o nome "Grupo de aplicações de ambiente de trabalho") é criado automaticamente sempre que cria um conjunto de anfitrião. Pode remover este grupo de aplicações em qualquer altura. No entanto, não é possível criar outro grupo de aplicações de ambiente de trabalho no conjunto de anfitrião, enquanto existe um grupo de aplicativo de desktop. Para publicar aplicativos remotos, tem de criar um grupo de aplicações do RemoteApp. Pode criar vários grupos de aplicações do RemoteApp para acomodar cenários de trabalhador diferente. Também podem conter diferentes grupos de aplicações do RemoteApp sobreposição RemoteApps.

Para publicar recursos aos utilizadores, tem de atribuí-las para grupos de aplicações. Ao atribuir utilizadores a grupos de aplicações, considere os seguintes procedimentos:

- Não é possível atribuir um utilizador para um grupo de aplicativo de desktop e um grupo de aplicações do RemoteApp no mesmo pool de anfitrião.
- Um utilizador pode ser atribuído a vários grupos de aplicações dentro do mesmo conjunto de anfitrião e o seu feed serão uma acumulação de ambos os grupos de aplicações.

## <a name="tenant-groups"></a>Grupos de inquilino

Na área de Trabalho Virtual de Windows, o inquilino de área de Trabalho Virtual do Windows é onde acontece a maior parte da instalação e configuração. O inquilino de área de Trabalho Virtual do Windows contém os conjuntos de anfitrião, grupos de aplicações e atribuições de utilizador de grupo de aplicações. No entanto, podem ser determinadas situações em que precisa de gerir vários inquilinos de área de Trabalho Virtual do Windows ao mesmo tempo, especialmente se for um fornecedor de serviços Cloud (CSP) ou um parceiro de alojamento. Nestas situações, pode utilizar um grupo de inquilino de área de Trabalho Virtual do Windows personalizado colocar cada um dos inquilinos de área de Trabalho Virtual do Windows dos clientes e gerir centralmente o acesso. No entanto, se estiver a gerir apenas um único inquilino de área de Trabalho Virtual do Windows, o conceito de grupo do inquilino não se aplica e pode continuar a operar e gerir o seu inquilino que existe no grupo de inquilino predefinido.

## <a name="end-users"></a>Utilizadores finais

Depois de atribuir utilizadores para os grupos de aplicações, pode ligar-se para uma implementação de área de Trabalho Virtual do Windows com qualquer um dos clientes de ambiente de Trabalho Virtual do Windows.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o acesso delegado e como atribuir funções aos utilizadores no [delegado acesso no Windows Virtual Desktop Preview](delegated-access-virtual-desktop.md).

Para saber como configurar o seu inquilino de área de Trabalho Virtual do Windows, veja [criar um inquilino no Windows Virtual Desktop Preview](tenant-setup-azure-active-directory.md).

Para saber como ligar à área de Trabalho Virtual do Windows, consulte um dos seguintes artigos:

- [Ligar para o cliente de ambiente de trabalho remoto no Windows 7 e Windows 10](connect-windows-7-and-10.md)
- [Ligar para o cliente de web do Windows Virtual Desktop pré-visualização](connect-web.md)
