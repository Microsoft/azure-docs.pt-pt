---
title: Autosserviço de senha reiniciada no Azure Ative Directory B2C | Microsoft Docs
description: Demonstra como configurar o reset da palavra-passe de autosserviço para os seus clientes no Azure Ative Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0c9edaf3356ea4c1a521a89f2ec60a4b6ba1a5ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87481500"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Configurar reset de senha de autosserviço para os seus clientes

Com a funcionalidade de reset da palavra-passe de autosserviço, os seus clientes que se inscreveram para contas locais podem redefinir as suas palavras-passe por conta própria. Isto reduz significativamente os encargos para o seu pessoal de apoio, especialmente se a sua aplicação tiver milhões de clientes a usá-lo regularmente. Atualmente, usar um endereço de e-mail verificado é o único método de recuperação suportado.

> [!NOTE]
> Este artigo aplica-se ao reset da palavra-passe de autosserviço utilizado no contexto do Sinal padrão **no** fluxo do utilizador, que utiliza **o SignIn da Conta Local** como fornecedor de identidade. Se necessitar de fluxos de utilizador totalmente personalizáveis na redefinição de palavras-passe invocados na sua aplicação, consulte [este artigo.](user-flow-overview.md)
>
>

Por predefinição, o seu diretório não tem a palavra-passe de autosserviço ligada. Utilize os seguintes passos para ligá-lo:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) como Administrador de Subscrição. Esta é a mesma conta de trabalho ou escola ou a mesma conta Microsoft que usou para criar o seu diretório.
2. Abra o **Diretório Ativo Azure** (na barra de navegação do lado esquerdo).
3. Desloque-se para baixo na lâmina de opções e selecione **o reset da palavra-passe**.
4. Definir **redefinição de senha de serviço de self-service ativada**  para **Todos**.
5. Clique em **Guardar** no início da página. Acabou-se!

Para testar, utilize a função "Run now" em qualquer fluxo de utilizador que tenha contas locais como fornecedor de identidade. Na página de inscrição da conta local (onde introduz um endereço de e-mail e senha, ou um nome de utilizador e senha), clique em Não pode aceder à **sua conta?**

> [!NOTE]
> As páginas de reset da palavra-passe de autosserviço podem ser personalizadas utilizando [a funcionalidade de marca da empresa.](../active-directory/fundamentals/customize-branding.md)
>
>

