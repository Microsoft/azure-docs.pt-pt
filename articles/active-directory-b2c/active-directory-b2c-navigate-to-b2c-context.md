---
title: Mudar para um inquilino de B2C no Azure Active Directory B2C | Documentos da Microsoft
description: Como mudar para o contexto do seu inquilino do Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 4/13/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 9315b3a5e1641098daf2c7dadf9fa4f09d0cb2a7
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57438513"
---
# <a name="switching-to-your-azure-ad-b2c-tenant"></a>Mudar para o inquilino do Azure AD B2C

Para configurar o Azure AD B2C, terá de estar no contexto do inquilino do Azure AD B2C.

## <a name="log-into-azure-ad-b2c-tenant"></a>Iniciar sessão no inquilino do Azure AD B2C

Para navegar para o inquilino do Azure AD B2C, deve ter sessão iniciada no portal do Azure como administrador global do inquilino do Azure AD B2C.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Altere os inquilinos ao clicar no endereço de correio eletrónico ou na imagem no canto superior direito.
1. Na lista `Directory` apresentada, selecione o inquilino do Azure AD B2C que pretende gerir.

O portal do Azure é atualizado.  Agora a sessão iniciada no Portal do Azure no contexto do inquilino do Azure AD B2C está iniciada.

## <a name="navigate-to-the-b2c-features-pane"></a>Navegar para o painel de funcionalidades do B2C

1. Clique em **Procurar** na navegação do lado esquerdo.
1. Clique em **Todos os serviços** e, em seguida, procure `Azure AD B2C` no painel de navegação à esquerda.  (Para afixar ao Startboard do lado esquerdo, clique na estrela à esquerda de Azure AD B2C)
1. Clique em **Azure AD B2C** para aceder ao painel de funcionalidades B2C.
   
    ![Captura de ecrã de navegar para o painel de funcionalidades do B2C](./media/active-directory-b2c-get-started/b2c-browse.png)

> [!IMPORTANT]
> Precisa de ser um Administrador Global de inquilino do B2C para poder aceder ao painel de funcionalidades do B2C. Um Administrador Global de qualquer outro inquilino ou um utilizador de qualquer outro inquilino não poderá aceder.  Pode mudar para o seu inquilino B2C ao utilizar o comutador do inquilino no canto superior direito do portal do Azure.
