---
title: Modificar os conteúdos da página no portal do programador na Gestão da API
titleSuffix: Azure API Management
description: Saiba como editar conteúdos de páginas no portal do programador na Gestão de API do Azure.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: vlvinogr
editor: ''
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/09/2017
ms.author: vlvinogr
ms.openlocfilehash: ebf2cbd430339378a09d10d91ad61327d24842e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75430636"
---
# <a name="modify-the-content-and-layout-of-pages-on-the-developer-portal-in-azure-api-management"></a>Modificar o conteúdo e o esquema das páginas no portal do programador na Gestão de API do Azure
Existem três formas fundamentais de personalizar o portal do programador na Gestão de API do Azure:

* [Editar o conteúdo de páginas estáticas e elementos de esquema da página][modify-content-layout] (explicado neste guia)
* [Atualizar os estilos utilizados para elementos de página em todo o portal do programador][customize-styles]
* [Modificar os modelos utilizados para páginas geradas pelo portal][portal-templates] (por exemplo, documentos de API, produtos, autenticação de utilizador, etc.)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="structure-of-developer-portal-pages"></a><a name="page-structure"> </a>Estrutura das páginas do portal do programador

O portal do programador baseia-se num sistema de gestão de conteúdos. O esquema de cada página é criado com base num conjunto de pequenos elementos de página conhecidos como widgets:

![Estrutura de páginas do portal do programador][api-management-customization-widget-structure]

Todos os widgets são editáveis.
* Os conteúdos principais específicos de cada página individual encontram-se no widget "Conteúdos". Editar uma página significa editar o conteúdo deste widget.
* Todos os elementos de esquema de página estão contidos com os restantes widgets. As alterações feitas a estes widgets são aplicadas a todas as páginas. Estes são referidos como "widgets de esquema."

Na edição diária de páginas, um indivíduo modificaria frequentemente apenas o widget Conteúdos, que terá conteúdo diferente para cada página individual.

## <a name="modifying-the-contents-of-a-layout-widget"></a><a name="modify-layout-widget"> </a>Modificar o conteúdo de um widget de esquema

O portal do Programador está acessível a partir do Portal do Azure.

1. Clique em **Portal do Programador** na barra de ferramentas da sua instância de Gestão de API.
2. Para editar os conteúdos dos widgets, clique no ícone composto por dois pincéis no menu do portal **Programador** à esquerda.
3. Para modificar os conteúdos do cabeçalho, desloque-se para a secção **Cabeçalho** na lista à esquerda.

    Os widgets são editáveis a partir dos campos.
4. Assim que estiver pronto para publicar as alterações, clique em **Publicar** na parte inferior da página.

Agora deve conseguir ver o novo cabeçalho em todas as páginas do portal do programador.

## <a name="next-steps"></a><a name="next-steps"> </a>Passos seguintes
* [Atualizar os estilos utilizados para elementos de página em todo o portal do programador][customize-styles]
* [Modificar os modelos utilizados para páginas geradas pelo portal][portal-templates] (por exemplo, documentos de API, produtos, autenticação de utilizador, etc.)

[Structure of developer portal pages]: #page-structure
[Modifying the contents of a layout widget]: #modify-layout-widget
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customization-widget-structure]: ./media/api-management-modify-content-layout/portal-widget-structure.png
[api-management-management-console]: ./media/api-management-modify-content-layout/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-modify-content-layout/api-management-widgets-header.png
[api-management-customization-manage-content]: ./media/api-management-modify-content-layout/api-management-customization-manage-content.png
