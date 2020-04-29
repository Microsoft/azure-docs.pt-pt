---
title: Quickstart - Ver aplicações de inquilinos usando o Diretório Ativo Azure
description: Neste Quickstart, utilize o portal Azure para visualizar as aplicações no seu inquilino azure Ative Directory (Azure AD).
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
ms.openlocfilehash: 1d40e968bb7079d50e3fa18889ae996c9b59c90f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74421811"
---
# <a name="quickstart-view-your-azure-active-directory-tenant-applications"></a>Quickstart: Veja as suas aplicações de inquilinos do Azure Ative Directory

Este início rápido utiliza o portal do Azure para ver as aplicações no seu inquilino do Azure Active Directory (Azure AD).

## <a name="before-you-begin"></a>Antes de começar

Para ver resultados, tem de ter pelo menos uma aplicação no inquilino do Azure AD. Para adicionar uma aplicação, veja o início rápido [Add an application](add-application-portal.md) (Adicionar uma aplicação).

Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador global do inquilino do Azure AD, administrador de uma aplicação na cloud ou administrador de uma aplicação.

## <a name="find-the-list-of-tenant-applications"></a>Localizar a lista de aplicações do inquilino

As aplicações do inquilino do Azure AD são visíveis na secção **Aplicações empresariais** do portal do Azure.

Para localizar as aplicações do inquilino:

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.
1. No painel **azure Ative Directory,** selecione **aplicações Enterprise**.
1. A partir do menu de entrega do **Tipo de Aplicação,** selecione **Todas as Aplicações,** e escolha **Aplicar**. É apresentada uma amostra aleatória das aplicações do seu inquilino.
1. Para ver mais aplicações, selecione **Carregar mais** na parte inferior da lista. Dependendo do número de aplicações no inquilino, poderá ser mais fácil [procurar uma aplicação específica](#search-for-a-tenant-application) em vez de percorrer a lista.

## <a name="select-viewing-options"></a>Selecionar as opções de visualização

Selecione opções de acordo com o que procura.

1. Pode visualizar as aplicações por Tipo de **Aplicação,** Estatuto de **Aplicação**e visibilidade da **aplicação.**
1. Em **Tipo de Aplicação**, escolha uma das seguintes opções:

    - **Aplicações Empresariais** mostra aplicações não Microsoft.
    - **Aplicações da Microsoft** mostra aplicações da Microsoft.
    - **Todas as Aplicações** mostra aplicações não Microsoft e Microsoft.

1. Em **Estado da Aplicação**, escolha **Qualquer**, **Desativada** ou **Ativada**. A opção **Qualquer** inclui aplicações desativadas e ativadas.
1. Em **Visibilidade da Aplicação**, escolha **Qualquer** ou **Oculta**. A opção **Oculta** mostra aplicações que estão no inquilino, mas não são visíveis para os utilizadores.
1. Depois de escolher as opções que deseja, selecione **Aplicar**.

## <a name="search-for-a-tenant-application"></a>Procurar uma aplicação do inquilino

Para procurar uma determinada aplicação:

1. No menu **Tipo de Aplicação,** selecione **Todas as aplicações,** e escolha **Aplicar**.
1. Introduza o nome da aplicação que quer encontrar. Se a aplicação tiver sido adicionada ao seu inquilino DaD Azure, aparece nos resultados da pesquisa. Este exemplo mostra que o GitHub não foi adicionado às aplicações de inquilinos.

    ![Exemplo mostra que uma aplicação não foi adicionada ao inquilino](media/view-applications-portal/search-for-tenant-application.png)

1. Experimente introduzir as primeiras letras do nome de uma aplicação. Este exemplo mostra todas as aplicações que começam com **Sales**.

    ![Exemplo mostra todas as aplicações que começam com vendas](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a ver as aplicações no seu inquilino Azure AD. Aprendeu a filtrar a lista de aplicações por tipo de aplicação, estado e visibilidade. Também aprendeu a procurar aplicações específicas.

Agora que encontrou a candidatura que procurava, pode continuar a [adicionar mais candidaturas ao seu inquilino.](add-application-portal.md) Ou, pode selecionar a aplicação para visualizar ou editar propriedades e opções de configuração. Por exemplo, pode configurar o início de sessão único.

> [!div class="nextstepaction"]
> [Configurar o início de sessão único](configure-single-sign-on-non-gallery-applications.md)
