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
ms.openlocfilehash: 7772b3ee5d0e27c09e83f7d118eb9f67f17e0d07
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523563"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Registar a aplicação com o inquilino do AD
Primeiro, Registre seu aplicativo com seu locatário do Azure Active Directory (Azure AD). Esta ação dar-lhe-á um ID de Aplicação para a aplicação e ativá-lo-á para receber tokens.

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
   
1. Escolha seu locatário do Azure AD selecionando sua conta no canto superior direito da página, seguido selecionando a navegação do **diretório switch** e, em seguida, selecionando o locatário apropriado. 
   - Ignore esta etapa se você tiver apenas um locatário do Azure AD em sua conta ou se você já tiver selecionado o locatário do Azure AD apropriado.
   
1. Na portal do Azure, procure e selecione **Azure Active Directory**.
   
1. No menu **Azure Active Directory** à esquerda, selecione **registros do aplicativo**e, em seguida, selecione **novo registro**.
   
1. Siga os avisos e crie uma nova aplicação. Não importa se é um aplicativo Web ou um cliente público (Mobile & Desktop) para este tutorial, mas se você quiser exemplos específicos para aplicativos Web ou aplicativos cliente públicos, Confira nossos [guias de início rápido](../articles/active-directory/develop/v1-overview.md).
   
   - **Nome** é o nome da aplicação e descreve a sua aplicação aos utilizadores finais.
   - Em **tipos de conta com suporte**, selecione **contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
   - Forneça o **URI de redirecionamento**. Para aplicativos Web, essa é a URL base do seu aplicativo onde os usuários podem entrar.  Por exemplo, `http://localhost:12345`. Para o cliente público (Mobile & Desktop), o AD do Azure o usa para retornar respostas de token. Insira um valor específico para seu aplicativo.  Por exemplo, `http://MyFirstAADApp`.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. Depois de concluir o registro, o Azure AD atribuirá ao seu aplicativo um identificador de cliente exclusivo (a **ID do aplicativo**). Você precisará desse valor nas próximas seções, portanto, copie-o da página do aplicativo.
   
1. Para localizar seu aplicativo no portal do Azure, selecione **registros de aplicativo**e, em seguida, selecione **Exibir todos os aplicativos**.
