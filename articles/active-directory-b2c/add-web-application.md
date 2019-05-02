---
title: Adicionar uma aplicação web - Azure Active Directory B2C | Documentos da Microsoft
description: Saiba como adicionar uma aplicação web com o seu inquilino do Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.author: davidmu
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: ede3fd0dd1d0351e691a9f160260c029d01c8f8a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64704032"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Adicionar uma web de aplicação de API com o seu inquilino do Azure Active Directory B2C

 Registe recursos de Web API no seu inquilino para que eles possam aceitar e responder a pedidos por aplicações cliente que apresentam um token de acesso. Este artigo mostra-lhe como registar a aplicação no Azure Active Directory (Azure AD) B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C. Selecione o **filtro de diretório e subscrição** no menu superior e selecione o diretório que contém o seu inquilino.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
4. Selecione **aplicativos**e, em seguida, selecione **Add**.
5. Introduza um nome para a aplicação. Por exemplo, *webapi1*.
6. Para **incluir a aplicação web / web API** e **permitir fluxo implícito**, selecione **Sim**.
7. Para **URL de resposta**, introduza um ponto de extremidade em que o Azure AD B2C deverá devolver quaisquer tokens que a aplicação solicite. Em seu aplicativo de produção, pode definir o URL de resposta para um valor como `https://localhost:44332`. Para fins de teste, definir o URL de resposta `https://jwt.ms`.
8. Para **URI de ID de aplicação**, introduza o identificador utilizado para a API web. O URI identificador completo, incluindo o domínio, é gerado por si. Por exemplo, `https://contosotenant.onmicrosoft.com/api`.
9. Clique em **Criar**.
10. Na página de propriedades, registe o ID da aplicação que irá utilizar quando configurar a aplicação web.

## <a name="configure-scopes"></a>Configurar âmbitos

Âmbitos proporcionam uma forma de reger o acesso a recursos protegidos. São utilizados pela API Web para implementar o controlo de acesso baseado no âmbito. Por exemplo, os utilizadores da API Web podem ter acesso de leitura e escrita ou podem ter apenas acesso só de leitura. Neste tutorial, vai utilizar os âmbitos para definir as permissões de leitura e escrita para a API Web.

1. Selecione **aplicativos**e, em seguida, selecione *webapi1*.
2. Selecione **âmbitos publicados**.
3. Para **âmbito**, introduza `Read`e para a descrição, insira `Read access to the application`.
4. Para **âmbito**, introduza `Write`e para a descrição, insira `Write access to the application`.
5. Clique em **Guardar**.

Os âmbitos publicados podem ser utilizados para conceder um cliente permissão da aplicação para a API web.

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API web protegida a partir de uma aplicação, tem de conceder as permissões de aplicação para a API. Por exemplo, no [Tutorial: Registar uma aplicação no Azure Active Directory B2C](tutorial-register-applications.md), um aplicativo web é criado no Azure AD B2C com o nome *webapp1*. Pode utilizar esta aplicação para chamar a API web.

1. Selecione **aplicativos**e, em seguida, selecione a sua aplicação web.
2. Selecione **acesso à API**e, em seguida, selecione **Add**.
3. Na **selecionar API** menu pendente, selecione *webapi1*.
4. Na **selecionar âmbitos** menu pendente, selecione a **leitura** e **escrever** âmbitos que definiu anteriormente.
5. Clique em **OK**.

A aplicação fica registada para chamar a API web protegida. Um utilizador é autenticado com o Azure AD B2C para utilizar a aplicação. A aplicação obtém uma concessão de autorização do Azure AD B2C para aceder à API web protegida.
