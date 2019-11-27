---
title: Logon vinculado para aplicativos do Azure AD-plataforma Microsoft Identity
description: Configurar o logon único vinculado (SSO) para seus aplicativos empresariais do Azure AD na plataforma Microsoft Identity (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c4547bddeea8b67bd3377124b1c299662fea3e6
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274173"
---
# <a name="configure-linked-sign-on"></a>Configurar o início de sessão ligado

Quando você adiciona um aplicativo Web galeria ou não Galeria, uma das opções de logon único disponíveis para você é o [logon vinculado](what-is-single-sign-on.md). Selecione esta opção para adicionar um link para o aplicativo no painel de acesso do Azure AD ou no portal do Office 365 da sua organização. Você pode usar esse método para adicionar links a aplicativos Web personalizados que atualmente usam Serviços de Federação do Active Directory (AD FS) (ou outro serviço de Federação) em vez do Azure AD para autenticação. Ou, você pode adicionar links profundos a páginas específicas do SharePoint ou a outras páginas da Web que você só deseja que apareçam nos painéis de acesso do usuário.

## <a name="before-you-begin"></a>Antes de começar

Se o aplicativo não tiver sido adicionado ao seu locatário do Azure AD, consulte [Adicionar um aplicativo da Galeria](add-gallery-app.md) ou [Adicionar um aplicativo inexistente na Galeria](add-non-gallery-app.md).

### <a name="open-the-app-and-select-linked-sign-on"></a>Abra o aplicativo e selecione logon vinculado

1. Entre no [portal do Azure](https://portal.azure.com) como um administrador de aplicativo de nuvem ou um administrador de aplicativo para seu locatário do Azure AD.

1. Navegue até **Azure Active Directory** > **aplicativos empresariais**. Uma amostra aleatória dos aplicativos no seu locatário do Azure AD é exibida. 

1. No menu **tipo de aplicativo** , selecione **todos os aplicativos**e, em seguida, selecione **aplicar**.

1. Insira o nome do aplicativo na caixa de pesquisa e, em seguida, selecione o aplicativo nos resultados.

1. Na seção **gerenciar** , selecione **logon único**. 

1. Selecione **vinculado**.

1. Insira a URL do aplicativo a ser vinculado. Digite a URL e selecione **salvar**. 
 
1. Você pode atribuir usuários e grupos ao aplicativo, o que faz com que o aplicativo seja exibido no [iniciador de aplicativos do Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou no [painel de acesso do Azure ad](end-user-experiences.md) para esses usuários.

1. Selecione **Guardar**.

## <a name="next-steps"></a>Passos Seguintes

- [Atribuir usuários ou grupos ao aplicativo](methods-for-assigning-users-and-groups.md)
- [Configurar o provisionamento automático de conta de usuário](configure-automatic-user-provisioning-portal.md)
