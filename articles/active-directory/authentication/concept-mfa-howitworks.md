---
title: Visão geral da autenticação de multi-factores azure
description: Saiba como a Autenticação Multi-Factor Azure ajuda a salvaguardar o acesso a dados e aplicações, ao mesmo tempo que satisfaz a procura do utilizador por um processo de entrada simples.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c50232abd12c8c0390409bd7bf72833b4f153e02
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667367"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Como funciona: Multi-Factor Authentication do Azure

A autenticação de vários fatores é um processo em que um utilizador é solicitado durante o processo de iniciar sessão para uma forma adicional de identificação, como introduzir um código no seu telemóvel ou fornecer uma digitalização de impressões digitais.

Se usar apenas uma palavra-passe para autenticar um utilizador, deixa um vetor inseguro para o ataque. Se a palavra-passe é fraca ou foi exposta noutro local, será realmente o utilizador a iniciar sessão com o nome de utilizador e a palavra-passe, ou se é um intruso? Quando se exige uma segunda forma de autenticação, a segurança é aumentada, uma vez que este fator adicional não é algo que seja fácil para um intruso obter ou duplicar.

![Imagem conceptual das diferentes formas de autenticação multifactor](./media/concept-mfa-howitworks/methods.png)

A autenticação multi-factor Azure funciona exigindo dois ou mais dos seguintes métodos de autenticação:

* Algo que sabe, tipicamente uma senha.
* Algo que tenha, como um dispositivo de confiança que não é facilmente duplicado, como uma chave de telefone ou hardware.
* Algo que tu és, biometria como uma impressão digital ou um exame facial.

Os utilizadores podem registar-se tanto para o reset de palavra-passe self-service como para a Autenticação Multi-Factor Azure num só passo para simplificar a experiência de embarque. Os administradores podem definir quais as formas de autenticação secundária que podem ser utilizadas. A autenticação de multi-factores Azure também pode ser exigida quando os utilizadores realizam uma redefinição da palavra-passe self-service para garantir mais esse processo.

![Métodos de autenticação em uso no ecrã de início de sessão](media/concept-authentication-methods/overview-login.png)

A Autenticação Multi-Factor Azure ajuda a salvaguardar o acesso a dados e aplicações, mantendo a simplicidade para os utilizadores. Proporciona segurança adicional exigindo uma segunda forma de autenticação e fornece autenticação forte através de uma gama de métodos de [autenticação](concept-authentication-methods.md)fáceis de usar. Os utilizadores podem ou não ser contestados para o MFA com base em decisões de configuração que um administrador toma.

As suas aplicações ou serviços não necessitam de fazer alterações para utilizar a Autenticação Multifactor Azure. As solicitações de verificação fazem parte do evento de entrada em ad-in Azure AD, que solicita e processa automaticamente o desafio mfa quando necessário.

## <a name="available-verification-methods"></a>Métodos de verificação disponíveis

Quando um utilizador faz o inserido numa aplicação ou serviço e receber um pedido de MFA, pode escolher entre um dos formulários registados de verificação adicional. Um administrador pode exigir o registo destes métodos de verificação de autenticação multi-factor Azure, ou o utilizador pode aceder ao seu próprio [Meu Perfil](https://myprofile.microsoft.com) para editar ou adicionar métodos de verificação.

As seguintes formas adicionais de verificação podem ser utilizadas com autenticação multi-factor Azure:

* Aplicação Microsoft Authenticator
* Ficha de hardware do JURAMENTO
* SMS
* Chamada de voz

## <a name="how-to-enable-and-use-azure-multi-factor-authentication"></a>Como ativar e utilizar a autenticação de multifactor Azure

Os utilizadores e grupos podem ser ativados para que a Autenticação Multi-Factor Azure indique uma verificação adicional durante o evento de início de sessão. [Os incumprimentos](../fundamentals/concept-fundamentals-security-defaults.md) de segurança estão disponíveis para todos os inquilinos da AD Azure para permitir rapidamente a utilização da aplicação Microsoft Authenticator para todos os utilizadores.

Para controlos mais granulares, as políticas de [Acesso Condicional](../conditional-access/overview.md) podem ser usadas para definir eventos ou aplicações que requerem MFA. Estas políticas podem permitir eventos regulares de entrada quando o utilizador está na rede corporativa ou num dispositivo registado, mas solicitam fatores de verificação adicionais quando remotos ou num dispositivo pessoal.

![Diagrama de como o Acesso Condicional funciona para garantir o processo de inscrição](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>Passos seguintes

Para conhecer o licenciamento, consulte [Funcionalidades e licenças para autenticação multi-factor Azure](concept-mfa-licensing.md).

Para ver o MFA em ação, ative a autenticação multi-factor azure para um conjunto de utilizadores de teste no seguinte tutorial:

> [!div class="nextstepaction"]
> [Ativar a Multi-Factor Authentication do Azure](tutorial-mfa-applications.md)
