---
title: Ver aplicações do inquilino - Azure Active Directory | Microsoft Docs
description: Utilize o portal do Azure para ver as aplicações no seu inquilino do Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/25/2018
ms.author: celested
ms.reviewer: arvinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ca57746d1b13d2020f78d3c527e7d0c7a444769
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918404"
---
# <a name="view-your-azure-active-directory-tenant-applications"></a>Ver as aplicações do seu inquilino do Azure Active Directory

Este início rápido utiliza o portal do Azure para ver as aplicações no seu inquilino do Azure Active Directory (Azure AD).

## <a name="before-you-begin"></a>Antes de começar

Para ver resultados, tem de ter pelo menos uma aplicação no inquilino do Azure AD. Para adicionar uma aplicação, veja o início rápido [Add an application](add-application-portal.md) (Adicionar uma aplicação).

Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador global do inquilino do Azure AD, administrador de uma aplicação na cloud ou administrador de uma aplicação.

## <a name="find-the-list-of-tenant-applications"></a>Localizar a lista de aplicações do inquilino

As aplicações do inquilino do Azure AD são visíveis na secção **Aplicações empresariais** do portal do Azure.

Para localizar as aplicações do inquilino:

1. Na  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, selecione **Azure Active Directory**. 

2. Na **do Azure Active Directory** painel, selecione **aplicações empresariais**. 

3. Do **tipo de aplicação** menu pendente, selecione **todos os aplicativos**e escolha **aplicar**. É apresentada uma amostra aleatória das aplicações do seu inquilino.
   
4. Para ver mais aplicações, selecione **carregar mais** na parte inferior da lista. Dependendo do número de aplicações no inquilino, poderá ser mais fácil [procurar uma aplicação específica](#search-for-a-tenant-application) em vez de percorrer a lista.

## <a name="select-viewing-options"></a>Selecionar as opções de visualização

Selecione as opções de acordo com o que está procurando.

1. Pode ver as aplicações por **tipo de aplicação**, **estado da aplicação**, e **visibilidade de aplicativo**. 

2. Em **Tipo de Aplicação**, escolha uma das seguintes opções:

    - **Aplicações Empresariais** mostra aplicações não Microsoft.
    - **Aplicações da Microsoft** mostra aplicações da Microsoft.
    - **Todas as Aplicações** mostra aplicações não Microsoft e Microsoft.

3. Em **Estado da Aplicação**, escolha **Qualquer**, **Desativada** ou **Ativada**. A opção **Qualquer** inclui aplicações desativadas e ativadas.

4. Em **Visibilidade da Aplicação**, escolha **Qualquer** ou **Oculta**. O **Hidden** opção mostra aplicações que estiverem no inquilino, mas não são visíveis para os utilizadores.

5. Depois de escolher as opções que pretende, selecione **aplicar**.
 

## <a name="search-for-a-tenant-application"></a>Procurar uma aplicação do inquilino

Para procurar um determinado aplicativo:

1. Na **tipo de aplicação** menu, selecione **todos os aplicativos**e escolha **aplicar**.

2. Introduza o nome da aplicação que quer encontrar. Se a aplicação foi adicionada ao seu inquilino do Azure AD, ele é apresentada nos resultados da pesquisa. Este exemplo mostra que o GitHub não tenha sido adicionado para as aplicações de inquilino.

    ![Procurar uma aplicação](media/view-applications-portal/search-for-tenant-application.png)

3. Experimente introduzir as primeiras letras do nome de uma aplicação. Este exemplo mostra todas as aplicações que começam com **Sales**.

    ![Procurar com prefixo](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu como ver as aplicações no inquilino do Azure AD. Aprendeu a filtrar a lista de aplicações ao tipo de aplicação, o estado e visibilidade. Também aprendeu a procurar aplicações específicas.

Agora que descobriu que a aplicação foram à procura, pode continuar a [adicionar mais aplicações com o seu inquilino](add-application-portal.md). Em alternativa, pode selecionar a aplicação para ver ou editar as propriedades e opções de configuração. Por exemplo, pode configurar o início de sessão único. 

> [!div class="nextstepaction"]
> [Configurar o início de sessão único](configure-single-sign-on-portal.md)


