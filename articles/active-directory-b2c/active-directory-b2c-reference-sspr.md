---
title: Self-service reposição palavra-passe no Azure Active Directory B2C | Documentos da Microsoft
description: Demonstra como configurar o self-service reposição palavra-passe para os seus clientes no Azure Active Directory B2C
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e8137bc0e75595b5f548584bb2d1644de2e0fecd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508912"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Configurar o self-service reposição palavra-passe para os seus clientes

Com a funcionalidade de reposição de palavra-passe self-service, os clientes que se inscreveram para contas locais podem repor as palavras-passe por conta própria. Esta opção reduz significativamente a carga sobre a equipe de suporte, especialmente se o seu aplicativo tiver milhões de clientes usá-lo regularmente. Atualmente, a utilizar um endereço de correio eletrónico verificado é o método de recuperação suportados apenas.

> [!NOTE]
> Este artigo aplica-se a palavra-passe self-service reposição utilizada no contexto do V1 **iniciar sessão** fluxo de utilizador, que usa **início de sessão de conta Local** como o fornecedor de identidade. Se precisar de fluxos de utilizador de reposição de palavra-passe totalmente personalizável invocados a partir da sua aplicação, veja [este artigo](active-directory-b2c-reference-policies.md).
> 
> 

Por predefinição, o diretório não ter a palavra-passe self-service reposição ativada. Utilize os seguintes passos para ativá-la:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com/) como o administrador da subscrição. Este é o mesmo ou conta profissional ou a mesma conta Microsoft que utilizou para criar o seu diretório.
2. Open **do Azure Active Directory** (na barra de navegação no lado esquerdo).
4. Definir **auto-reposição de palavra-passe ativada** ao **todos os**. 
5. Clique em **Guardar** no início da página. Já está!

Para testar, utilize a funcionalidade de "Executar agora" em qualquer fluxo de início de sessão do utilizador que tem contas locais como fornecedor de identidade. Sobre a início de sessão na conta local página (onde introduzir um endereço de e-mail e palavra-passe, ou um nome de utilizador e palavra-passe), clique em **não é possível aceder à sua conta?** para verificar a experiência do cliente.

> [!NOTE]
> As páginas de reposição de palavra-passe self-service podem ser personalizadas utilizando a [funcionalidade de imagem corporativa](../active-directory/fundamentals/customize-branding.md).
> 
> 

