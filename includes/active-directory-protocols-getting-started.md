---
title: Descrição geral do Protocolo .NET do Azure AD | Microsoft Docs
description: Como utilizar mensagens HTTP para autorizar o acesso a aplicações Web e APIs Web no inquilino utilizando o Azure AD.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2019
ms.author: priyamo
ms.openlocfilehash: b6dd4cd55755ae2c92afd327ad72ffe6966b9a07
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121522"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Registar a aplicação com o inquilino do AD
Em primeiro lugar, terá de registar a sua aplicação com o seu inquilino do Azure Active Directory (Azure AD). Esta ação dar-lhe-á um ID de Aplicação para a aplicação e ativá-lo-á para receber tokens.

* Inicie sessão no [portal do Azure](https://portal.azure.com).
* Escolha o seu inquilino do Azure AD ao clicar na sua conta no canto superior direito da página, em seguida, clicando no **trocar diretório** navegação e, em seguida, selecione o inquilino adequado. 
  * Ignore este passo, se tiver apenas um inquilino do Azure AD na sua conta ou se já tiver selecionado o inquilino adequado do Azure AD.
* No painel de navegação da esquerda, clique em **Azure Active Directory**.
* Clique em **registos de aplicações** e clique em **novo registo**.
* Siga os avisos e crie uma nova aplicação. Ele não importa se é uma aplicação web ou um aplicativo cliente público (móvel e ambiente de trabalho) para este tutorial, mas se pretender exemplos específicos de aplicações web ou aplicações de cliente público, confira nossos [inícios Rápidos](../articles/active-directory/develop/v1-overview.md).
  * **Nome** é o nome da aplicação e descreve a sua aplicação aos utilizadores finais.
  * Sob **tipos de conta suportados**, selecione **contas em qualquer diretório organizacional e contas Microsoft pessoais**.
  * Forneça o **URI de redirecionamento**. Para aplicações Web, este é o URL base da sua aplicação onde os utilizadores podem iniciar sessão.  Por exemplo, `http://localhost:12345`. Para o cliente público (móvel e ambiente de trabalho), do Azure AD utiliza para devolver respostas token. Introduza um valor específico para a sua aplicação.  Por exemplo, `http://MyFirstAADApp`.
    <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
* Depois de concluir o registo, Azure AD atribuirá seu aplicativo um identificador de cliente exclusivo (os **ID da aplicação**). Este valor é necessário nas secções seguintes, por isso, copie-o partir da página de aplicativo.
* Para localizar seu aplicativo no portal do Azure, clique em **registos de aplicações**e, em seguida, clique em **ver todas as aplicações**.
