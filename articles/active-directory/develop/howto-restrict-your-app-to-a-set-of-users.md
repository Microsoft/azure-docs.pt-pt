---
title: Como restringir seu aplicativo registrado do Azure Active Directory para um conjunto de utilizadores
description: Saiba como restringir o acesso às suas aplicações registadas no Azure AD para um conjunto selecionado de utilizadores.
services: active-directory
documentationcenter: ''
author: kalyankrishna1
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e07d9f0fa6ec6b4abc7ce96279b7b02faae298fa
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540165"
---
# <a name="how-to-restrict-your-app-to-a-set-of-users"></a>Como: Restringir a aplicação a um conjunto de utilizadores

Aplicações registadas num inquilino do Azure Active Directory (Azure AD) são, por predefinição, disponível para todos os utilizadores do inquilino que a autenticação com êxito.

Da mesma forma, no caso de um [multi-inquilino](howto-convert-app-to-be-multi-tenant.md) aplicação, todos os utilizadores no inquilino do Azure AD em que esta aplicação está aprovisionada vão conseguir aceder a esta aplicação depois de se autenticar com êxito no seu respetivo inquilino.

Os administradores de inquilinos e os desenvolvedores geralmente têm requisitos onde uma aplicação tem de ser restrita a um determinado conjunto de utilizadores. Os desenvolvedores podem realizar o mesmo usando padrões de autorização populares, como com base em acesso controlo funções (RBAC), mas essa abordagem exige uma quantidade significativa de trabalho por parte do desenvolvedor.

O Azure AD permite inquilino, os administradores e desenvolvedores restringir uma aplicação a um conjunto específico de utilizadores ou grupos de segurança no inquilino.

## <a name="supported-app-configurations"></a>Configurações de aplicações suportadas

A opção de restringir uma aplicação a um conjunto específico de utilizadores ou grupos de segurança num inquilino funciona com os seguintes tipos de aplicativos:

- Aplicações configuradas para início de sessão único Federado com autenticação baseada em SAML
- Aplicações de proxy de aplicações que utilizam a pré-autenticação do Azure AD
- Os aplicativos criados diretamente na plataforma de aplicações do Azure AD que utilizam OAuth 2.0/OpenID Connect autenticação após um utilizador ou um administrador já autorizou a essa aplicação.

     > [!NOTE]
     > Esta funcionalidade está disponível para a web app/web API e aplicações empresariais apenas. Aplicações que estão registadas como [nativo](quickstart-v1-integrate-apps-with-azure-ad.md) não pode ser restringida a um conjunto de utilizadores ou grupos de segurança no inquilino.

## <a name="update-the-app-to-enable-user-assignment"></a>Atualizar a aplicação para permitir a atribuição de utilizadores

1. Vá para o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**
1. Na barra superior, selecione a conta com sessão iniciada. 
1. Sob **Directory**, selecione o inquilino do Azure AD em que a aplicação será registada.
1. No painel de navegação à esquerda, selecione **do Azure Active Directory**. Se o Azure Active Directory não está disponível no painel de navegação, em seguida, siga estes passos:

    1. Selecione **todos os serviços** na parte superior do menu de navegação esquerdo principal.
    1. Escreva **do Azure Active Directory** na caixa de pesquisa de filtro e, em seguida, selecione a **Azure Active Directory** item do resultado.

1. Na **do Azure Active Directory** painel, selecione **aplicações empresariais** do **Azure Active Directory** menu de navegação esquerdo.
1. Selecione **todos os aplicativos** para ver uma lista de todas as suas aplicações.

     Se não vir a aplicação que quer mostrar aqui, utilize os vários filtros na parte superior a **todos os aplicativos** lista para restringir a lista ou desloque-se para baixo na lista para localizar seu aplicativo.

1. Selecione a aplicação que pretende atribuir um utilizador ou grupo de segurança a partir da lista.
1. O aplicativo **descrição geral** página, selecione **propriedades** no menu de navegação do lado esquerdo da aplicação.
1. Localize a definição **atribuição utiliz. necessária?** e defina-a **Sim**. Quando esta opção está definida como **Sim**, em seguida, os usuários primeiro tem de ser atribuídos a esta aplicação antes de poderem aceder ao mesmo.
1. Selecione **guardar** para guardar esta alteração de configuração.

## <a name="assign-users-and-groups-to-the-app"></a>Atribuir utilizadores e grupos à aplicação

Depois de configurar a aplicação para ativar a atribuição de utilizadores, pode ir em frente e atribuir utilizadores e grupos à aplicação.

1. Selecione o **utilizadores e grupos** painel no menu de navegação esquerdo do aplicativo.
1. Na parte superior a **utilizadores e grupos** lista, selecione a **adicionar utilizador** botão para abrir o **adicionar atribuição** painel.
1. Selecione o **usuários** Seletor da **adicionar atribuição** painel. 

     Será apresentada uma lista de utilizadores e grupos de segurança, juntamente com uma caixa de texto para procurar e localizar um determinado usuário ou grupo. Este ecrã permite-lhe selecionar vários utilizadores e grupos de uma só vez.

1. Depois de concluída a selecionar os utilizadores e grupos, prima a **selecione** botão na parte inferior para avançar para a parte seguinte.
1. Prima a **atribuir** botão na parte inferior para concluir as atribuições de utilizadores e grupos para a aplicação. 
1. Confirme que os utilizadores e grupos que adicionou estão a aparecer no atualizada **utilizadores e grupos** lista.

