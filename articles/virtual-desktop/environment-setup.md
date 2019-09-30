---
title: Ambiente de área de trabalho virtual do Windows – Azure
description: Os elementos básicos de um ambiente de área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: helohr
ms.openlocfilehash: 23bf9be8e3e5f1c52546faa9ed5171c140eba59a
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676628"
---
# <a name="windows-virtual-desktop-environment"></a>Ambiente do Windows Virtual Desktop

A área de trabalho virtual do Windows é um serviço que oferece aos usuários acesso fácil e seguro a seus desktops e RemoteApps virtualizados. Este tópico lhe dirá um pouco mais sobre a estrutura geral do ambiente de área de trabalho virtual do Windows.

## <a name="tenants"></a>locatários

O locatário da área de trabalho virtual do Windows é a interface principal para gerenciar seu ambiente de área de trabalho virtual do Windows. Cada locatário da área de trabalho virtual do Windows deve ser associado ao Azure Active Directory que contém os usuários que entrarão no ambiente. No locatário da área de trabalho virtual do Windows, você pode começar a criar pools de hosts para executar cargas de trabalho dos usuários.

## <a name="host-pools"></a>Pools de hosts

Um pool de hosts é uma coleção de máquinas virtuais do Azure que se registram na área de trabalho virtual do Windows como hosts de sessão ao executar o agente de área de trabalho virtual do Windows. Todas as máquinas virtuais do host de sessão em um pool de hosts devem ser originadas da mesma imagem para uma experiência de usuário consistente.

Um pool de hosts pode ser um dos dois tipos:

- Pessoal, onde cada host de sessão é atribuído a usuários individuais.
- Em pool, onde os hosts de sessão podem aceitar conexões de qualquer usuário autorizado a um grupo de aplicativos dentro do pool de hosts.

Você pode definir propriedades adicionais no pool de hosts para alterar seu comportamento de balanceamento de carga, quantas sessões cada host de sessão pode executar e o que o usuário pode fazer para os hosts de sessão no pool de hosts enquanto entra em suas sessões de área de trabalho virtual do Windows. Você controla os recursos publicados para os usuários por meio de grupos de aplicativos.

## <a name="app-groups"></a>Grupos de aplicações

Um grupo de aplicativos é um agrupamento lógico de aplicativos instalados em hosts de sessão no pool de hosts. Um grupo de aplicativos pode ser de um dos dois tipos:

- RemoteApp, onde os usuários acessam os RemoteApps que você seleciona individualmente e publica no grupo de aplicativos
- Área de trabalho, onde os usuários acessam a área de trabalho completa

Por padrão, um grupo de aplicativos de desktop (denominado "grupo de aplicativos de área de trabalho") é criado automaticamente sempre que você cria um pool de hosts. Você pode remover esse grupo de aplicativos a qualquer momento. No entanto, você não pode criar outro grupo de aplicativos de área de trabalho no pool de hosts enquanto houver um grupo de aplicativos de desktop. Para publicar RemoteApps, você deve criar um grupo de aplicativos do RemoteApp. Você pode criar vários grupos de aplicativos do RemoteApp para acomodar diferentes cenários de trabalho. Grupos diferentes de aplicativos do RemoteApp também podem conter RemoteApps sobrepostos.

Para publicar recursos para os usuários, você deve atribuí-los a grupos de aplicativos. Ao atribuir usuários a grupos de aplicativos, considere as seguintes coisas:

- Um usuário não pode ser atribuído a um grupo de aplicativos de desktop e a um grupo de aplicativos do RemoteApp no mesmo pool de hosts.
- Um usuário pode ser atribuído a vários grupos de aplicativos dentro do mesmo pool de hosts, e seu feed será um acúmulo de ambos os grupos de aplicativos.

## <a name="tenant-groups"></a>Grupos de locatários

Na área de trabalho virtual do Windows, o locatário da área de trabalho virtual do Windows é onde ocorre a maior parte da configuração e da configuração. O locatário da área de trabalho virtual do Windows contém os pools de hosts, grupos de aplicativos e atribuições de usuário do grupo de aplicativos. No entanto, pode haver algumas situações em que você precisa gerenciar vários locatários de área de trabalho virtual do Windows de uma vez, especialmente se você for um provedor de serviços de nuvem (CSP) ou um parceiro de hospedagem. Nessas situações, você pode usar um grupo personalizado de locatários da área de trabalho virtual do Windows para posicionar os locatários de área de trabalho virtual do Windows dos clientes e gerenciar o acesso centralmente. No entanto, se você estiver gerenciando apenas um único locatário da área de trabalho virtual do Windows, o conceito do grupo de locatários não se aplicará e você poderá continuar a operar e gerenciar seu locatário que existe no grupo de locatários padrão.

## <a name="end-users"></a>Usuários finais

Depois de atribuir usuários aos seus grupos de aplicativos, eles podem se conectar a uma implantação de área de trabalho virtual do Windows com qualquer um dos clientes de área de trabalho virtual do Windows.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o acesso delegado e como atribuir funções a usuários em [acesso delegado na área de trabalho virtual do Windows](delegated-access-virtual-desktop.md).

Para saber como configurar seu locatário de área de trabalho virtual do Windows, confira [criar um locatário na área de trabalho virtual do Windows](tenant-setup-azure-active-directory.md).

Para saber como se conectar à área de trabalho virtual do Windows, consulte um dos seguintes artigos:

- [Conecte-se do Windows 10 ou Windows 7](connect-windows-7-and-10.md)
- [Conectar-se de um navegador da Web](connect-web.md)
