---
title: Adicionar uma aplicação cliente nativa - Azure Active Directory B2C | Documentos da Microsoft
description: Saiba como adicionar uma aplicação cliente nativa com o seu inquilino do Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 3f988e61c152be820e7e490e13908fb4ab54e6c5
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673397"
---
# <a name="add-a-native-client-application-to-your-azure-active-directory-b2c-tenant"></a>Adicionar uma aplicação cliente nativa com o seu inquilino do Azure Active Directory B2C

Recursos de cliente nativo têm de estar registado no seu inquilino para a aplicação pode comunicar com o Azure Active Directory B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
1. Selecione **aplicativos**e, em seguida, selecione **Add**.
2. Introduza um nome para a aplicação. Por exemplo, *nativeapp1*.
3. Para **incluir a aplicação web / web API**, selecione **não**.
4. Para **incluir cliente nativo**, selecione **Sim**.
5. Para **URI de redirecionamento**, introduza um URI de redirecionamento válido com um esquema personalizado. Existem duas considerações importantes ao escolher um URI de redirecionamento:

    - **Exclusivo** -o esquema do URI de redirecionamento deve ser exclusivo para cada aplicação. No exemplo `com.onmicrosoft.contoso.appname://redirect/path`, `com.onmicrosoft.contoso.appname` é o esquema. Este padrão deve ser seguido. Se duas aplicações partilharem o mesmo esquema, o utilizador recebe uma opção para escolher uma aplicação. Se o utilizador fizer uma seleção incorreta, o início de sessão irá falhar.
    - **Completa** -o URI de redirecionamento tem de ter um esquema e um caminho. O caminho tem de conter, pelo menos, uma barra depois do domínio. Por exemplo, `//contoso/` funciona e `//contoso` falhar. Certifique-se de que o URI de redirecionamento não inclui caracteres especiais, como carateres de sublinhado.

6. Clique em **Criar**.
7. Na página de propriedades, registe o ID da aplicação que irá utilizar quando configurar a sua aplicação de cliente nativo.
