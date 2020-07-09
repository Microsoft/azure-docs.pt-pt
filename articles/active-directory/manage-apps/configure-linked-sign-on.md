---
title: Linked-on para aplicações AD Azure - plataforma de identidade da Microsoft
description: Configurar um único sign-on (SSO) ligado às suas aplicações empresariais AZure AD na plataforma de identidade microsoft (Azure AD)
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 05/08/2019
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cfcece43ae1b7d7bcf0c38feba14f1e82b29f18
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763487"
---
# <a name="configure-linked-sign-on"></a>Configurar o início de sessão ligado

Quando adiciona uma aplicação web de galeria ou não galeria, uma das opções de inscrição única disponíveis para si está [ligada ao sign-on](what-is-single-sign-on.md). Selecione esta opção para adicionar um link à aplicação no Painel de Acesso AD AD da sua organização ou no portal Office 365. Pode utilizar este método para adicionar links a aplicações web personalizadas que atualmente utilizam serviços da Federação de Diretórios Ativos (ou outro serviço de federação) em vez de Azure AD para autenticação. Ou, pode adicionar links profundos a páginas específicas do SharePoint ou a outras páginas web que apenas pretende aparecer nos Painéis de Acesso do utilizador.

## <a name="before-you-begin"></a>Before you begin

Se a aplicação não tiver sido adicionada ao seu inquilino Azure AD, consulte [Adicionar uma aplicação](add-gallery-app.md) de galeria ou [adicionar uma aplicação não-galeria](add-non-gallery-app.md).

### <a name="open-the-app-and-select-linked-sign-on"></a>Abra a aplicação e selecione o sign-on ligado

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador de aplicações em nuvem ou como administrador de aplicação para o seu inquilino AZure AD.

1. Navegue para aplicações **da Azure Ative Directory**  >  **Enterprise**. Aparece uma amostra aleatória das aplicações no seu inquilino AZure AD. 

1. No menu **'Tipo de Aplicação',** selecione **Todas as aplicações**e, em seguida, selecione **Aplicar**.

1. Introduza o nome da aplicação na caixa de pesquisa e, em seguida, selecione a aplicação a partir dos resultados.

1. Na secção **'Gerir',** selecione **'Único's sign-on'**. 

1. Selecione **Linked**.

1. Introduza o URL da aplicação para ligar. Digite o URL e **selecione Guardar**. 
 
1. Pode atribuir utilizadores e grupos à aplicação, o que faz com que a aplicação apareça no [lançador de aplicações office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou no [painel de acesso AZure AD](end-user-experiences.md) para esses utilizadores.

1. Selecione **Guardar**.

## <a name="next-steps"></a>Próximos passos

- [Atribuir utilizadores ou grupos à aplicação](methods-for-assigning-users-and-groups.md)
- [Configurar o provisionamento automático da conta de utilizador](../app-provisioning/configure-automatic-user-provisioning-portal.md)
