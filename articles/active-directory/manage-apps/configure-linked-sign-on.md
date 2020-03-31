---
title: Inscrição ligada para aplicações DaD Azure - plataforma de identidade microsoft
description: Configure o único sign-on ligado (SSO) às suas aplicações empresariais Azure AD na plataforma de identidade da Microsoft (Azure AD)
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
ms.openlocfilehash: dfe4aeb17f482cc9d4126efc6d65d3f7d173536b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063548"
---
# <a name="configure-linked-sign-on"></a>Configurar o início de sessão ligado

Quando adiciona uma galeria ou aplicação web não-galeria, uma das opções únicas de inscrição disponível para si está ligada ao [sign-on](what-is-single-sign-on.md). Selecione esta opção para adicionar um link à aplicação no Painel de Acesso Azure AD da sua organização ou no portal Office 365. Pode utilizar este método para adicionar links a aplicações web personalizadas que atualmente utilizam serviços da Federação de Diretórios Ativos (ou outro serviço da federação) em vez de Azure AD para autenticação. Ou pode adicionar links profundos a páginas específicas do SharePoint ou a outras páginas web que apenas deseja aparecer nos Painéis de Acesso do utilizador.

## <a name="before-you-begin"></a>Antes de começar

Se a aplicação não tiver sido adicionada ao seu inquilino DaD Azure, consulte [Adicionar uma aplicação](add-gallery-app.md) de galeria ou [adicionar uma aplicação não-galeria](add-non-gallery-app.md).

### <a name="open-the-app-and-select-linked-sign-on"></a>Abra a aplicação e selecione sign-on linked

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador de aplicação em nuvem ou administrador de candidatura para o seu inquilino Azure AD.

1. Navegue para aplicações **da Azure Ative Directory** > **Enterprise.** Aparece uma amostra aleatória das aplicações no seu inquilino DaD Azure. 

1. No menu **Tipo de Aplicação,** selecione **Todas as aplicações,** e, em seguida, selecione **Aplicar**.

1. Introduza o nome da aplicação na caixa de pesquisa e, em seguida, selecione a aplicação a partir dos resultados.

1. Na secção **Gerir,** selecione **single sign-on**. 

1. Selecione **Linked**.

1. Introduza o URL da aplicação para ligar. Digite o URL e selecione **Guardar**. 
 
1. Pode atribuir utilizadores e grupos à aplicação, o que faz com que a aplicação apareça no lançador de [aplicações do Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou no painel de [acesso Azure AD](end-user-experiences.md) para esses utilizadores.

1. Selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

- [Atribuir utilizadores ou grupos à aplicação](methods-for-assigning-users-and-groups.md)
- [Configurar o fornecimento automático de conta de utilizador](../app-provisioning/configure-automatic-user-provisioning-portal.md)
