---
title: Redefinição de senha de autoatendimento no Azure Active Directory B2C | Microsoft Docs
description: Demonstra como configurar a redefinição de senha de autoatendimento para seus clientes no Azure Active Directory B2C
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7903ec669772c3a3858a9c1d514ab3e6de6a2bd4
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936819"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Configurar a redefinição de senha de autoatendimento para seus clientes

Com o recurso de redefinição de senha de autoatendimento, os clientes que se inscreveram para contas locais podem redefinir suas senhas por conta própria. Isso reduz significativamente a carga de sua equipe de suporte, especialmente se o seu aplicativo tiver milhões de clientes usando isso regularmente. Atualmente, o uso de um endereço de email verificado é o único método de recuperação com suporte.

> [!NOTE]
> Este artigo se aplica à redefinição de senha de autoatendimento usada no contexto do fluxo de usuário de **conexão** v1, que usa a **entrada de conta local** como o provedor de identidade. Se você precisar de fluxos de usuário de redefinição de senha totalmente personalizáveis invocados do seu aplicativo, consulte [Este artigo](active-directory-b2c-reference-policies.md).
> 
> 

Por padrão, seu diretório não tem a redefinição de senha de autoatendimento ativada. Use as etapas a seguir para ativá-lo:

1. Entre no [portal do Azure](https://portal.azure.com/) como o administrador da assinatura. Essa é a mesma conta corporativa ou de estudante ou a mesma conta Microsoft que você usou para criar seu diretório.
2. Abra **Azure Active Directory** (na barra de navegação no lado esquerdo).
3. Role para baixo na folha opções e selecione **redefinição de senha**.
4. Definir a **redefinição de senha de autoatendimento habilitada** para **todos**. 
5. Clique em **Guardar** no início da página. Você terminou!

Para testar, use o recurso "executar agora" em qualquer fluxo de usuário de entrada que tenha contas locais como um provedor de identidade. Na página de entrada da conta local (em que você insere um endereço de email e uma senha, ou um nome de usuário e uma senha), clique em **não é possível acessar sua conta?** para verificar a experiência do cliente.

> [!NOTE]
> As páginas de redefinição de senha de autoatendimento podem ser personalizadas usando o [recurso de identidade visual da empresa](../active-directory/fundamentals/customize-branding.md).
> 
> 

