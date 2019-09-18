---
title: Adicionar um aplicativo cliente nativo-Azure Active Directory B2C | Microsoft Docs
description: Saiba como adicionar um aplicativo cliente nativo ao seu locatário Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6fc953d5c6109fbc6eacbd946ecd112db4639fa5
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064582"
---
# <a name="add-a-native-client-application-to-your-azure-active-directory-b2c-tenant"></a>Adicionar um aplicativo cliente nativo ao seu locatário do Azure Active Directory B2C

Os recursos nativos do cliente precisam ser registrados em seu locatário antes que seu aplicativo possa se comunicar com Azure Active Directory B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
1. Selecione **aplicativos**e, em seguida, selecione **Adicionar**.
2. Insira um nome para o aplicativo. Por exemplo, *nativeapp1*.
3. Para **incluir aplicativo Web/API Web**, selecione **não**.
4. Para **incluir cliente nativo**, selecione **Sim**.
5. Para **URI**de redirecionamento, insira um URI de redirecionamento válido com um esquema personalizado. Há duas considerações importantes ao escolher um URI de redirecionamento:

    - **Unique** – o esquema do URI de redirecionamento deve ser exclusivo para cada aplicativo. No exemplo `com.onmicrosoft.contoso.appname://redirect/path`, `com.onmicrosoft.contoso.appname` é o esquema. Esse padrão deve ser seguido. Se dois aplicativos compartilharem o mesmo esquema, o usuário terá a opção de escolher um aplicativo. Se o usuário fizer uma escolha incorreta, a entrada falhará.
    - **Complete** -o URI de redirecionamento deve ter um esquema e um caminho. O caminho deve conter pelo menos uma barra após o domínio. Por exemplo, `//contoso/` funciona e `//contoso` falha. Certifique-se de que o URI de redirecionamento não inclua caracteres especiais, como sublinhados.

6. Clique em **Criar**.
7. Na página Propriedades, registre a ID do aplicativo que você usará ao configurar seu aplicativo cliente nativo.
