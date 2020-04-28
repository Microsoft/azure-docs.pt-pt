---
title: Reset de senha de autosserviço no Diretório Ativo Azure B2C Microsoft Docs
description: Demonstra como configurar o reset de senha de autosserviço para os seus clientes no Azure Ative Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d6dad52c8a3e63c64bb8e0e0030e8c50b5bab42c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78183113"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Configurar o reset de palavra-passe self-service para os seus clientes

Com a funcionalidade de redefinição da palavra-passe self-service, os seus clientes que se inscreveram para contas locais podem redefinir as suas palavras-passe por conta própria. Isto reduz significativamente os encargos para o seu pessoal de suporte, especialmente se a sua aplicação tiver milhões de clientes a usá-lo regularmente. Atualmente, a utilização de um endereço de e-mail verificado é o único método de recuperação suportado.

> [!NOTE]
> Este artigo aplica-se ao reset de palavra-passe de self-service utilizado no contexto do Sinal V1 **no** fluxo do utilizador, que utiliza o **SignIn** de Conta Local como fornecedor de identidade. Se necessitar de redefinir os fluxos de utilizador de redefinição de senha totalmente personalizáveis invocados a partir da sua aplicação, consulte [este artigo](user-flow-overview.md).
>
>

Por predefinição, o seu diretório não tem a palavra-passe de autosserviço ligada. Utilize os seguintes passos para ligá-lo:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) como Administrador de Subscrição. Esta é a mesma conta de trabalho ou escola ou a mesma conta microsoft que usou para criar o seu diretório.
2. Open **Azure Ative Diretório** (na barra de navegação do lado esquerdo).
3. Desloque-se para baixo na lâmina de opções e selecione **reset password**.
4. Definir Reprograma da **palavra-passe** do self service ativado a **Todos**.
5. Clique em **Guardar** no início da página. Acabou-se!

Para testar, utilize a funcionalidade "Run now" em qualquer fluxo de utilizador de entrada que tenha contas locais como fornecedor de identidade. Na página de entrada da conta local (onde introduz um endereço de e-mail e senha, ou um nome de utilizador e senha), clique em Não aceder à **sua conta para** verificar a experiência do cliente.

> [!NOTE]
> As páginas de reset de palavra-passe de autosserviço podem ser personalizadas utilizando a funcionalidade de marca da [empresa](../active-directory/fundamentals/customize-branding.md).
>
>

