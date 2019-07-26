---
title: Ver aplicações do inquilino - Azure Active Directory | Microsoft Docs
description: Utilize o portal do Azure para ver as aplicações no seu inquilino do Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 764c7c048dd0398b495ee31421a8e2bc9f8169ee
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477187"
---
# <a name="view-your-azure-active-directory-tenant-applications"></a>Ver as aplicações do seu inquilino do Azure Active Directory

Este início rápido utiliza o portal do Azure para ver as aplicações no seu inquilino do Azure Active Directory (Azure AD).

## <a name="before-you-begin"></a>Antes de começar

Para ver resultados, tem de ter pelo menos uma aplicação no inquilino do Azure AD. Para adicionar uma aplicação, veja o início rápido [Add an application](add-application-portal.md) (Adicionar uma aplicação).

Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador global do inquilino do Azure AD, administrador de uma aplicação na cloud ou administrador de uma aplicação.

## <a name="find-the-list-of-tenant-applications"></a>Localizar a lista de aplicações do inquilino

As aplicações do inquilino do Azure AD são visíveis na secção **Aplicações empresariais** do portal do Azure.

Para localizar as aplicações do inquilino:

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.
1. No painel de **Azure Active Directory** , selecione **aplicativos empresariais**.
1. No menu suspenso **tipo de aplicativo** , selecione **todos os aplicativos**e escolha **aplicar**. É apresentada uma amostra aleatória das aplicações do seu inquilino.
1. Para exibir mais aplicativos, selecione **carregar mais** na parte inferior da lista. Dependendo do número de aplicações no inquilino, poderá ser mais fácil [procurar uma aplicação específica](#search-for-a-tenant-application) em vez de percorrer a lista.

## <a name="select-viewing-options"></a>Selecionar as opções de visualização

Selecione opções de acordo com o que você está procurando.

1. Você pode exibir os aplicativos por **tipo de aplicativo**, **status do aplicativo**e **visibilidade do aplicativo**.
1. Em **Tipo de Aplicação**, escolha uma das seguintes opções:

    - **Aplicações Empresariais** mostra aplicações não Microsoft.
    - **Aplicações da Microsoft** mostra aplicações da Microsoft.
    - **Todas as Aplicações** mostra aplicações não Microsoft e Microsoft.

1. Em **Estado da Aplicação**, escolha **Qualquer**, **Desativada** ou **Ativada**. A opção **Qualquer** inclui aplicações desativadas e ativadas.
1. Em **Visibilidade da Aplicação**, escolha **Qualquer** ou **Oculta**. A opção **Hidden** mostra os aplicativos que estão no locatário, mas não são visíveis para os usuários.
1. Depois de escolher as opções desejadas, selecione **aplicar**.

## <a name="search-for-a-tenant-application"></a>Procurar uma aplicação do inquilino

Para procurar um aplicativo específico:

1. No menu **tipo de aplicativo** , selecione **todos os aplicativos**e escolha **aplicar**.
1. Introduza o nome da aplicação que quer encontrar. Se o aplicativo tiver sido adicionado ao seu locatário do Azure AD, ele aparecerá nos resultados da pesquisa. Este exemplo mostra que o GitHub não foi adicionado aos aplicativos de locatário.

    ![O exemplo mostra que um aplicativo não foi adicionado ao locatário](media/view-applications-portal/search-for-tenant-application.png)

1. Experimente introduzir as primeiras letras do nome de uma aplicação. Este exemplo mostra todas as aplicações que começam com **Sales**.

    ![Exemplo mostra todos os aplicativos que começam com vendas](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você aprendeu a exibir os aplicativos em seu locatário do Azure AD. Você aprendeu a filtrar a lista de aplicativos por tipo de aplicativo, status e visibilidade. Também aprendeu a procurar aplicações específicas.

Agora que você encontrou o aplicativo que estava procurando, pode continuar a [adicionar mais aplicativos ao seu locatário](add-application-portal.md). Ou, você pode selecionar o aplicativo para exibir ou editar propriedades e opções de configuração. Por exemplo, pode configurar o início de sessão único.

> [!div class="nextstepaction"]
> [Configure single sign-on](configure-single-sign-on-non-gallery-applications.md) (Configurar o início de sessão único)
