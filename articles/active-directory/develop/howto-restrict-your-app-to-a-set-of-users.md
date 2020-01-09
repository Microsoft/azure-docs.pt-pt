---
title: Restringir o aplicativo Azure AD a um conjunto de usuários | Azure
titleSuffix: Microsoft identity platform
description: Saiba como restringir o acesso aos seus aplicativos registrados no Azure AD para um conjunto selecionado de usuários.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff6e9d101159af33a05a2dc50e227bc97970b12d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424466"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users"></a>Como restringir seu aplicativo do Azure AD a um conjunto de usuários

Os aplicativos registrados em um locatário Azure Active Directory (Azure AD) são, por padrão, disponíveis para todos os usuários do locatário que são autenticados com êxito.

Da mesma forma, no caso de um aplicativo [multilocatário](howto-convert-app-to-be-multi-tenant.md) , todos os usuários no locatário do Azure AD em que esse aplicativo é provisionado poderão acessar esse aplicativo depois que eles se autenticarem com êxito em seu respectivo locatário.

Os administradores de locatários e os desenvolvedores geralmente têm requisitos em que um aplicativo deve ser restrito a um determinado conjunto de usuários. Os desenvolvedores podem fazer o mesmo usando padrões de autorização populares, como o RBAC (controle de acesso baseado em função), mas essa abordagem requer uma quantidade significativa de trabalho em parte do desenvolvedor.

O Azure AD permite que os administradores de locatários e os desenvolvedores restrinjam um aplicativo a um conjunto específico de usuários ou grupos de segurança no locatário.

## <a name="supported-app-configurations"></a>Configurações de aplicativo com suporte

A opção de restringir um aplicativo a um conjunto específico de usuários ou grupos de segurança em um locatário funciona com os seguintes tipos de aplicativos:

- Aplicativos configurados para logon único federado com autenticação baseada em SAML
- Aplicativos de proxy de aplicativo que usam pré-autenticação do Azure AD
- Aplicativos criados diretamente na plataforma de aplicativo do Azure AD que usam a autenticação OAuth 2.0/OpenID Connect depois que um usuário ou administrador consentiu nesse aplicativo.

     > [!NOTE]
     > Esse recurso está disponível somente para aplicativos Web/API Web e aplicativo corporativo. Os aplicativos registrados como [nativos](quickstart-v1-integrate-apps-with-azure-ad.md) não podem ser restritos a um conjunto de usuários ou grupos de segurança no locatário.

## <a name="update-the-app-to-enable-user-assignment"></a>Atualizar o aplicativo para habilitar a atribuição de usuário

Há duas maneiras de criar um aplicativo com atribuição de usuário habilitada. Uma delas requer a função de **administrador global** , a segunda não.

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>Aplicativos empresariais (requer a função de administrador global)

1. Vá para a [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global**.
1. Na barra superior, selecione a conta conectada. 
1. Em **diretório**, selecione o locatário do Azure AD em que o aplicativo será registrado.
1. Na barra de navegação à esquerda, selecione **Azure Active Directory**. Se Azure Active Directory não estiver disponível no painel de navegação, siga estas etapas:

    1. Selecione **todos os serviços** na parte superior do menu de navegação esquerdo principal.
    1. Digite **Azure Active Directory** na caixa de pesquisa de filtro e, em seguida, selecione o item de **Azure Active Directory** do resultado.

1. No painel de **Azure Active Directory** , selecione **aplicativos empresariais** no **Azure Active Directory** menu de navegação à esquerda.
1. Selecione **todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

     Se você não vir o aplicativo que deseja exibir aqui, use os vários filtros na parte superior da lista **todos os aplicativos** para restringir a lista ou role para baixo na lista para localizar seu aplicativo.

1. Selecione o aplicativo para o qual você deseja atribuir um usuário ou grupo de segurança na lista.
1. Na página **visão geral** do aplicativo, selecione **Propriedades** no menu de navegação esquerdo do aplicativo.
1. Localize a configuração **atribuição de usuário necessária?** e defina-a como **Sim**. Quando essa opção é definida como **Sim**, os usuários devem primeiro ser atribuídos a esse aplicativo antes de poderem acessá-lo.
1. Selecione **salvar** para salvar essa alteração de configuração.

### <a name="app-registration"></a>Registo da aplicação

1. Vá para a [**portal do Azure**](https://portal.azure.com/).
1. Na barra superior, selecione a conta conectada. 
1. Em **diretório**, selecione o locatário do Azure AD em que o aplicativo será registrado.
1. Na barra de navegação à esquerda, selecione **Azure Active Directory**.
1. No painel de **Azure Active Directory** , selecione **registros de aplicativo** no menu de navegação do **Azure Active Directory** esquerdo.
1. Crie ou selecione o aplicativo que você deseja gerenciar. Você precisa ser **proprietário** deste registro de aplicativo.
1. Na página **visão geral** do aplicativo, siga o link **aplicativo gerenciado no diretório local** sob o Essentials na parte superior da página. Isso levará você para o _aplicativo empresarial gerenciado_ do registro do aplicativo.
1. Na folha de navegação à esquerda, selecione **Propriedades**.
1. Localize a configuração **atribuição de usuário necessária?** e defina-a como **Sim**. Quando essa opção é definida como **Sim**, os usuários devem primeiro ser atribuídos a esse aplicativo antes de poderem acessá-lo.
1. Selecione **salvar** para salvar essa alteração de configuração.

## <a name="assign-users-and-groups-to-the-app"></a>Atribuir usuários e grupos ao aplicativo

Depois de configurar seu aplicativo para habilitar a atribuição de usuário, você pode prosseguir e atribuir usuários e grupos ao aplicativo.

1. Selecione o painel **usuários e grupos** no menu de navegação esquerdo do aplicativo.
1. Na parte superior da lista **usuários e grupos** , selecione o botão **Adicionar usuário** para abrir o painel **Adicionar atribuição** .
1. Selecione o seletor **usuários** no painel **Adicionar atribuição** . 

     Uma lista de usuários e grupos de segurança será mostrada junto com uma caixa de texto para pesquisar e localizar um determinado usuário ou grupo. Esta tela permite que você selecione vários usuários e grupos em um só lugar.

1. Quando terminar de selecionar os usuários e grupos, pressione o botão **selecionar** na parte inferior para ir para a próxima parte.
1. Pressione o botão **atribuir** na parte inferior para concluir as atribuições de usuários e grupos ao aplicativo. 
1. Confirme se os usuários e grupos que você adicionou estão aparecendo na lista **usuários e grupos** atualizados.

